<template>
  <div>
    <div class="container">
      <div class="space"></div>

      <div class="slider-container">
        <div
          class="item"
          v-for="item in items"
          :ref="'item' + item"
          :key="item"
          :data-item="item"
          @mouseleave="leaveItem"
        >
          <div class="thumbnail" @mouseenter="enterItem(item)">
            <img
              src="https://www.infobae.com/new-resizer/gwaND9ofHqQLwrJrNNwiHqTrjpY=/768x432/filters:format(jpg):quality(85)/s3.amazonaws.com/arc-wordpress-client-uploads/infobae-wp/wp-content/uploads/2017/05/12115047/homero-simpson-1920-1024x575.jpg"
              alt="foto de portada de video"
            />
          </div>

          <div
            class="detailed-view-container"
            :class="{
              'from-left': item == 1,
              'from-right': item == items.length,
            }"
            v-if="
              currentItem == item &&
              (state === 'hovered' || state === 'expanded')
            "
            @click.prevent="clickVideo"
          >
            <div class="hovered-view relative">
              <div class="thumbnail">
                <img
                  src="https://www.infobae.com/new-resizer/gwaND9ofHqQLwrJrNNwiHqTrjpY=/768x432/filters:format(jpg):quality(85)/s3.amazonaws.com/arc-wordpress-client-uploads/infobae-wp/wp-content/uploads/2017/05/12115047/homero-simpson-1920-1024x575.jpg"
                  alt="foto de portada de video"
                />
              </div>

              <div class="video-container" v-show="videoLoaded">
                <video src="../../public/video/1.mp4" autoplay></video>
              </div>

              <div
                class="action-icons"
                v-if="state === 'hovered' || state === 'expanded'"
              >
                <div class="action-icons-left">
                  <div class="play-button" v-if="state === 'expanded'">
                    Reproducir
                  </div>
                  <span class="material-icons" v-if="state !== 'expanded'">
                    play_circle
                  </span>
                  <span class="material-icons"> add_circle_outline </span>
                  <span class="material-icons"> thumb_up_alt </span>
                  <span class="material-icons"> thumb_down_alt </span>
                </div>
                <div class="action-icons-right" v-if="state === 'hovered'">
                  <span class="material-icons"> expand_more </span>
                </div>
              </div>
            </div>

            <div class="expanded-view">
              <div class="left">
                <h2>Un título</h2>
                <p>
                  Lorem ipsum dolor sit amet consectetur adipisicing elit. Sequi
                  et, earum praesentium adipisci dolores nostrum velit. Eos,
                  magnam quibusdam totam aperiam aut corrupti sed excepturi
                  iure, beatae mollitia ducimus culpa? Lorem ipsum dolor sit
                  amet consectetur adipisicing elit. Sequi et, earum praesentium
                  adipisci dolores nostrum velit. Eos, magnam quibusdam totam
                  aperiam aut corrupti sed excepturi iure, beatae mollitia
                  ducimus culpa?
                </p>
              </div>
              <div class="right">
                <p>Instructor: <span class="highlight">Antonio Hopkins</span></p>
                <p>Fecha de publicación: <span class="highlight">1984</span></p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="backdrop" @click.self="reset" v-if="state === 'expanded'"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      state: "initial",
      currentItem: 1,
      currentItemNode: "",
      currentDetailedViewContainerNode: "",
      items: [1, 2, 3, 4],
      videoLoaded: false,
      mousePath: "",
    };
  },

  mounted() {
    document.onmousemove = this.handleMouseMove;
    document.onclick = this.handleMouseClick;
  },

  methods: {
    handleMouseMove(event) {
      this.mousePath = event.path;
    },

    handleMouseClick(event) {
      console.log("clicked", event.x);
    },

    //Al terminar la transición de hover out, se fija si está parado en otro item, de se así, lo anima:
    checkMousePath() {
      for (let i = 0; i < this.mousePath.length - 5; i++) {
        if (this.mousePath[i].classList.contains("item")) {
          this.enterItem(this.mousePath[i].dataset.item);
          break;
        }
      }
    },

    enterItem(item) {
      // return

      if (this.state !== "initial") return;

      this.currentItem = item;
      this.state = "hovered";

      const ref = "item" + item;
      this.currentItemNode = this.$refs[ref][0];

      const nodes = this.currentItemNode.childNodes;

      this.$nextTick(() => {
        for (var i = 0; i < nodes.length; i++) {
          if (nodes[i].classList.contains("detailed-view-container")) {
            let rect = this.currentItemNode.getBoundingClientRect();

            nodes[i].style.top = `${rect.top}px`;

            if (item == 1) {
              nodes[i].style.left = `${rect.left}px`;
            } else if (item == this.items.length) {
              nodes[i].style.right = `${window.innerWidth - rect.right}px`;
            } else {
              nodes[i].style.left = `${rect.left - 40}px`;
            }

            this.currentDetailedViewContainerNode = nodes[i];
            break;
          }
        }

        setTimeout(() => {
          this.currentItemNode.classList.add("hovered");
        }, 600);
      });

      //simula la llamada al backend para obtener el video
      setTimeout(() => {
        this.videoLoaded = true;
        setTimeout(() => {
          this.currentDetailedViewContainerNode.classList.add("fade-in");
        }, 100);
      }, 1000);
    },

    leaveItem(item) {
      // return;

      if (this.state !== "hovered") return;

      this.currentDetailedViewContainerNode.classList.remove("fade-in");
      this.currentItemNode.classList.remove("hovered");

      setTimeout(() => {
        this.state = "initial";
        this.videoLoaded = false;
        this.currentItem = null;
        this.checkMousePath();
      }, 300);
    },

    clickVideo(item) {
      this.state = "expanded";
      this.currentItemNode.classList.remove("hovered");
      this.currentItemNode.classList.add("expanded");
    },

    reset() {
      this.currentItemNode.classList.remove("expanded");
      this.currentDetailedViewContainerNode.classList.remove("fade-in");
      setTimeout(() => {
        this.state = "initial";
      }, 300);
    },
  },
};
</script>

<style scoped lang="scss">
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1em;
}

.slider-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1em;
}

.item {
  width: 320px;
  height: 180px;
  position: relative;
  transition: all 0.3s ease-out;
}

// HOVERED VIEW:

.detailed-view-container {
  width: 400px;
  position: fixed;
  transform: scaleX(0.8) scaleY(0.8);
  transform-origin: top center;
  z-index: 20;
  transition: all 0.3s ease-out;
}

.item.hovered .detailed-view-container {
  transition: all 0.3s ease-out;
  transform: scaleX(1) scaleY(1) translateY(-40px);
}

.from-left {
  transform-origin: top left;
}

.from-right {
  transform-origin: top right;
}

.video-container {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
}

video {
  opacity: 0;
  transition: opacity 0.4s ease-out;
}

.detailed-view-container.fade-in video {
  opacity: 1;
}

// EXPANDED VIEW:

.item.expanded .detailed-view-container {
  top: 2em !important; //porque se le agrega estilo in-line que tiene prioridad
  left: 50% !important;
  transform: translateX(-50%);
  width: 700px;
}

.expanded-view {
  display: none;
  background: black;
  transition: all 0.3 ease-out;
}

.item.expanded .expanded-view {
  display: flex;
  padding: 1em;
  .left {
    h2 {
      margin-bottom: 0.5em;
    }
    padding-right: 2em;
    width: 60%;
  }
  .right {
    p {
      margin-bottom: 0.5em;
    }
  }
}

// ACTION ICONS:

.action-icons {
  opacity: 0;
  position: absolute;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 2;
  background: black;
  padding: 0.5em;
  transition: all 0.3s ease-out;
}

.item.hovered .action-icons {
  opacity: 1;
}

.item.expanded .action-icons {
  opacity: 1;
  background: rgba(0, 0, 0, 0.527);
  bottom: 0;
  padding: 1em;
}

.action-icons-left {
  display: flex;
  align-items: center;
  .material-icons {
    margin-right: 0.2em;
  }
}

.play-button {
  font-size: 1.8rem;
  background: white;
  color: black;
  border-radius: 7px;
  padding: 0.5em;
  margin-right: 0.4em;
  cursor: pointer;
}

.material-icons {
  font-size: 3rem;
  cursor: pointer;
}

.material-icons:hover {
  color: coral;
}

.play-button:hover {
  background: coral;
}

// position fixed y overflow hidden al CONTAINER para que no scrollee y sólo scrollee el detailed view

.space {
  height: 500px;
}
</style>
