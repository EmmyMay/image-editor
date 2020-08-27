<template>
  <div class="home">
    
    <div ref="board" class="board"></div>

    <!-- ******* The below section is for the file input and the temp gallery -->
    <v-row>
      <v-col cols="12">
        <v-file-input
          v-model="files"
          accept="image/.svg, image/.jpg, image/.png"
          multiple
         
          id="file"
          ref="files"
        ></v-file-input>
      </v-col>
      <v-btn @click="image" class="white--text" label="Choose a picture" color="black">Submit</v-btn>
    </v-row>
    <v-col ref="gallery" class="display">
      <h4>Your Pictures</h4>
      <figure ref="figure"></figure>
    </v-col>
  </div>
</template>

<script>
  // using the interact JS library
  import interact from "interactjs";
  export default {
    name: "Home",
    components: {},
    data() {
      return {
        files: [],
      };
    },
    methods: {
      image() {
          // A postion object that will handle target image's dynamic positioning
        let pos = {
          x: 0,
          y:0
        }

        // Board is the div that holds the resizable image
        let board = document.querySelector(".board");
        let figure = this.$refs.figure; //Element under which each image will be attached

        for (let i = 0; i < this.files.length; i++) {
          var element = this.files[i];

          var pic = URL.createObjectURL(element); //creating a blob of the image file
          // making an image element onto which each img blob will be loaded and attached to the  figure tag
          let img = document.createElement("img"); 
          img.width = 300;
          img.height = 300;
          img.src = pic;
          figure.appendChild(img);

          // appending an event listener to each image to be loaded onto the board when clicked
          img.addEventListener("click", function (e) {
            console.log(e);
            !this.picKed;

            // making a clone of each image tag whenever it is cloned
            var newImg = e.target.cloneNode(true);
            board.appendChild(newImg); //img gets attached to the board when clicked
            // interact js logic
            interact(newImg).resizable({
              edges: { left: true, right: true, bottom: true, top: true },
              listeners: {
                move(e) {
                  var target = e.target;
                  var x = parseFloat(target.getAttribute("data-x")) || 0;
                  var y = parseFloat(target.getAttribute("data-y")) || 0;

                  // update the element's style
                  target.style.width = e.rect.width + "px";
                  target.style.height = e.rect.height + "px";

                  // translate when resizing from top or left edges
                  x += e.deltaRect.left;
                  y += e.deltaRect.top;

                  target.style.webkitTransform = target.style.transform =
                    "translate(" + x + "px," + y + "px)";

                  target.setAttribute("data-x", x);
                  target.setAttribute("data-y", y);
                  target.textContent =
                    Math.round(e.rect.width) +
                    "\u00D7" +
                    Math.round(e.rect.height);
                },
                modifiers: [
                  // keep the edges inside the parent
                  interact.modifiers.restrictEdges({
                    outer: "parent",
                  }),

                  // minimum size
                  interact.modifiers.restrictSize({
                    min: { width: 100, height: 50 },
                  }),
                ],

                inertia: true,
              },
            }) .draggable({
    listeners: { 
      move(e){
        console.log(e.target);
         pos.x += e.dx
      pos.y += e.dy

      e.target.style.transform =
        `translate(${pos.x}px, ${pos.y}px)`
      }
     },
    inertia: true,
    modifiers: [
      interact.modifiers.restrictRect({
        restriction: 'parent',
        endOnly: true
      })
    ]
  })
          });
        }
      },
      
    },
    mounted() {},
  };
</script>

<style lang="scss" scoped>
.home {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  .display {
    display: flex;
    border: 2px red solid;

    figure {
      display: flex;
      margin-top: 4rem;
    }
  }
  .board {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 90vw;
    height: 70vh;
    pos: relative;
    margin-top: 5rem;

    border: 2px yellow solid;
  }
}
</style>
