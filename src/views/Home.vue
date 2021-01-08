<template>
  <div>
    <div class="container">
      <div class="space"></div>

      <div class="slider-container">
        <div
          v-for="(item, i) in items"
          class="item"
          :key="i"
          :data-item="i"
          :ref="'item' + i"
          @mouseleave="leaveItem"
        >
          <div class="thumbnail" @mouseenter="enterItem(i)">
            <img
              src="https://www.infobae.com/new-resizer/gwaND9ofHqQLwrJrNNwiHqTrjpY=/768x432/filters:format(jpg):quality(85)/s3.amazonaws.com/arc-wordpress-client-uploads/infobae-wp/wp-content/uploads/2017/05/12115047/homero-simpson-1920-1024x575.jpg"
              alt="foto de portada de video"
            />
          </div>

          <div
            class="detailed-view-container"
            :class="{
              'from-left': i == 0,
              'from-right': i == items.length - 1,
            }"
            v-if="
              currentItem == i && (state === 'hovered' || state === 'expanded')
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
                <h2>{{ item.title }}</h2>
                <p>
                  {{ item.description }}
                </p>
              </div>
              <div class="right">
                <p>
                  Autor/a: <span class="highlight">{{ item.author }}</span>
                </p>
                <p>
                  Fecha de publicación:
                  <span class="highlight">{{ item.publcationDate.toLocaleDateString('es-AR') }}</span>
                </p>
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
      loading: false,
      state: "initial",
      currentItem: 0,
      currentItemNode: "",
      currentDetailedViewContainerNode: "",
      items: [
        {
          imgUrl: "",
          videoUrl: "../../public/video/1.mp4",
          title: "Película 1",
          description: "Descripción 1",
          author: "Autor 1",
          publcationDate: new Date(),
        },
        {
          imgUrl: "",
          videoUrl: "",
          title: "Película 2",
          description: "Descripción 2",
          author: "Autor 2",
          publcationDate: new Date(),
        },
        {
          imgUrl: "",
          videoUrl: "",
          title: "Película 3",
          description: "Descripción 3",
          author: "Autor 3",
          publcationDate: new Date(),
        },
      ],

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
      console.log("clicked, x pos:", event.x);
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

    enterItem(index) {
      // return

      if (this.state !== "initial" || this.loading) return;

      this.currentItem = index;
      this.state = "hovered";

      const ref = "item" + index;
      this.currentItemNode = this.$refs[ref][0];

      const nodes = this.currentItemNode.childNodes;
      this.loading = true;

      this.$nextTick(() => {
        for (var i = 0; i < nodes.length; i++) {
          if (nodes[i].classList.contains("detailed-view-container")) {
            let rect = this.currentItemNode.getBoundingClientRect();

            nodes[i].style.top = `${rect.top}px`;

            if (index == 0) {
              nodes[i].style.left = `${rect.left}px`;
            } else if (index == this.items.length - 1) {
              nodes[i].style.right = `${window.innerWidth - rect.right}px`;
            } else {
              nodes[i].style.left = `${rect.left - 40}px`;
            }

            this.currentDetailedViewContainerNode = nodes[i];
            break;
          }
        }

        setTimeout(() => {
          if (this.loading) {
            this.currentItemNode.classList.add("hovered");
          }
          this.loading = false;
        }, 400);
      });

      //simula la llamada al backend para obtener el video
      setTimeout(() => {
        this.videoLoaded = true;
        setTimeout(() => {
          this.currentDetailedViewContainerNode.classList.add("fade-in");
        }, 100);
      }, 2000);
    },

    leaveItem(item) {
      if (this.loading) {
        this.state = "initial";
        this.currentItem = null;
        this.loading = false;
        return;
      }

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

      setTimeout(() => {
        this.currentItemNode.classList.add("expanded-normal-scale");
      }, 300);
    },

    reset() {
      this.currentItemNode.classList.remove("expanded");
      this.currentItemNode.classList.remove("expanded-normal-scale");
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
  scale: 0.6;
  transform: translateX(-50%);
  width: 700px;
  transition: all 0.3s ease-out;
}

.item.expanded-normal-scale .detailed-view-container {
  transform: translateX(-50%) scale(1);
}

.expanded-view {
  display: none;
  background: black;
  transition: all 0.3 ease-out;
}

.item.expanded .expanded-view {
  opacity: 0;
  display: flex;
  padding: 1em;
  transition: opacity 0.5s ease-out;
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

//c1720679

.item.expanded-normal-scale .expanded-view {
  opacity: 1;
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
