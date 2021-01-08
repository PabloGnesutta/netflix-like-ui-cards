<template>
  <div>
    <div class="container">
      <div class="space"></div>
      <div class="items-container">
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
            v-if="
              currentItem == item &&
              (state === 'hovered' || state === 'expanded')
            "
            @click.prevent="clickVideo"
          >
            <div class="detailed-view relative">
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

            <div
              class="expanded-view"
              v-if="currentItem == item && state === 'expanded'"
            >
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
                <p>Instructor: <span class="highlight">Marcelo Tinelli</span></p>
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
      items: [1, 2, 3],
      videoLoaded: false,
      mousePath: "",
    };
  },

  mounted() {
    document.onmousemove = this.handleMouseMove;
    document.onclick = this.handleMouseClick;
    // console.log('withd', )
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
        setTimeout(() => {
          this.currentItemNode.classList.add("hovered");
        }, 10);

        for (var i = 0; i < nodes.length; i++) {
          if (nodes[i].classList.contains("detailed-view-container")) {
            let rect = this.currentItemNode.getBoundingClientRect();

            //ver si es primer, último, o ítem del medio para ajustar posición

            nodes[i].style.top = `${rect.top}px`;

            if (item == 1) {
              nodes[i].style.left = `${rect.left}px`;
            } else if (item == 2) {
              nodes[i].classList.add("translate");
            } else if (item == 3) {
              nodes[i].style.right = `${window.innerWidth - rect.right}px`;
            }

            this.currentDetailedViewContainerNode = nodes[i];
            break;
          }
        }
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

      this.currentDetailedViewContainerNode.classList.remove("translate");
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
.translate {
  transform: translateX(-40px);
}
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1em;
}

.items-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1em;
}

.item {
  width: 320px;
  min-height: 0px;
  position: relative;
  transition: all 0.3s ease-out;
  background: black;
}

// HOVERED VIEW:

.detailed-view-container {
  width: 320px;
  min-height: 200px;
  position: fixed;
  background: black;
  transition: all 0.3s ease-out;
  z-index: 20;
  
}

.item.hovered .detailed-view-container {
  transition: all 0.3s ease-out;
  width: 400px;
  min-height: 300px;
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
  min-height: 800px;
}

.expanded-view {
  display: none;
}

.item.expanded .expanded-view {
  padding: 1em;
  display: flex;
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
  background: black;
  position: relative;
  width: 100%;
  padding: 0.5em;
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 2;
}

.item.expanded .action-icons {
  background: rgba(0, 0, 0, 0.527);
  position: absolute;
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
