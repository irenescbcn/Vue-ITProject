<template>
    <div>
        <!--TO GET THE INITIAL DATA-->
        <div class="initialData">
            <!--SQUARE DIMENSIONS-->
            <h5>DIMENSIONS</h5>
            <div class="row">                         
                <div class="col">
                    <label for="squareWidth">Width</label>
                    <input type="number" class="form-control" id="squareWidth" aria-describedby="squareWidthError" v-model="widthSquare">
                    <small id="squareWidthError" class="form-text text-muted">---</small>
                </div>
                <div class="col">
                    <label for="squareHeight">Height</label>
                    <input type="number" class="form-control" id="squareHeight" aria-describedby="squareHeightError" v-model="heightSquare">
                    <small id="squareHeightError" class="form-text text-muted">---</small>
                </div>
            </div>
            <!--ROVER COORDINATES-->
            <h5>COORDINATES</h5>
            <div class="row">                         
                <div class="col">
                    <label for="coordinateX">Coordinate X</label>
                    <input type="number" class="form-control" id="coordinateX" aria-describedby="coordinateXError" v-model="coordX">
                    <small id="coordinateXError" class="form-text text-muted">---</small>
                </div>
                <div class="col">
                    <label for="coordinateY">Coordinate Y</label>
                    <input type="number" class="form-control" id="coordinateY" aria-describedby="coordinateYError" v-model="coordY">
                    <small id="coordinateYError" class="form-text text-muted">---</small>
                </div>
                <div class="col">
                    <div class="form-group">
                        <label for="initialOrientation">Initial Orientation</label>
                        <select v-model="selectedOrientation" class="form-control" id="initialOrientation">
                            <option value="N">North</option>
                            <option value="S">South</option>
                            <option value="E">East</option>
                            <option value="W">West</option>
                        </select>
                    </div>
                </div>
            </div>
        </div>

        <!--TO GET THE INSTRUCTIONS-->
        <div class="commands">
            <h5>INSTRUCTIONS</h5>
            <div class="commandButtons mb-3">
                <button class="btn btn-dark" @click="commandsButton('L')">Turn Left (L)</button>
                <button class="btn btn-dark ml-2" @click="commandsButton('A')">Advance (A)</button>
                <button class="btn btn-dark ml-2" @click="commandsButton('R')">Turn Rigth (R)</button>
            </div>

            <div class="input-group">
                <input type="text" class="form-control" readonly="readonly" placeholder="Click on the buttons above" v-model="instructions">
                <div class="input-group-append">
                    <button class="btn btn-dark" type="button" @click="validateInputs()">Send</button>
                </div>
            </div>
            <p class="form-text text-danger">{{inputError}}</p>
        </div>

        <!--TO SHOW THE RESULTS-->
        
            <div class="finalResult"> 
                <p><strong>Final Coordinates: </strong>
                    <span v-show="showFinalCoordinates">{{finalCoordX}}, {{finalCoordY}}</span>
                </p>
                <p><strong>Final Orientation: </strong><span>{{finalOrientation}}</span></p>
                <p class="text-danger" v-show="showRoverError">{{roverError}}</p>
            </div>
            <div class="squareDiv">
                <div class="square" v-show="showRover" :style="{width: widthSquare*10 + 'px', height: heightSquare*10 + 'px'}">
                    <img src="@/assets/rover.png" class="roverSize" :style="{'margin-left': finalCoordX*10 + 'px', 'margin-bottom': finalCoordY*10 + 'px', transform: 'rotate(' + rotationNumber + 'deg)'}">
                </div>
            </div>
        
    </div>
</template>


<script>
export default {
  name: 'Rover',
  data(){
      return{
          instructions: [],
          //Initial Inputs
          selectedOrientation: "N",
          orientation: ["N","E","S","W"],
          widthSquare: Number,
          heightSquare: Number,
          coordX: Number,
          coordY: Number,
          inputError: "",
          //Final Result Inputs
          finalCoordX: Number,
          finalCoordY: Number,
          finalOrientation: "",
          showFinalCoordinates: false,
          roverError: "",
          showRover: false,
          showRoverError: false,
          rotationNumber: Number,
     
      }
  }, 
  methods:{
    commandsButton(command){
        switch(command){
            case "L":
                this.instructions.push("L");
            break;
            case "A":
                this.instructions.push("A");
            break;
            case "R":
                this.instructions.push("R");
            break;
        }
    },

    validateInputs(){ 
        if(this.widthSquare !== Number && this.heightSquare !== Number && this.coordX !== Number && this.coordY !== Number && 
        this.instructions !== []){
            //To validate Coords X and Y
            if(this.widthSquare < this.coordX || this.heightSquare < this.coordY ){
                this.inputError = "Coordinates are not valid. They exceed the area of the square"
            }//To check the size of the square
            else if(this.widthSquare > 99 || this.heightSquare > 99 || this.widthSquare < 30 || this.heightSquare < 30){
                 this.inputError = "The minimum width and height is 30 and the maximum is 99"
            }
            else{
                this.showRover = true
                let i; 
                this.inputError = "";
                //To check the initial orientation in case the commands do not change the initial orientation 
                if(this.selectedOrientation == "N"){
                    this.rotationNumber = 0
                }else if(this.selectedOrientation == "E"){
                    this.rotationNumber = 90
                }else if(this.selectedOrientation == "S"){
                    this.rotationNumber = 180
                }else{
                    this.rotationNumber = -90
                }

                //To check the final orientation
                for(i = 0; i < this.instructions.length; i++){
                    if(this.instructions[i] == "L"){
                        let j;
                        if(this.finalOrientation == ""){
                            for(j = 0; j < this.orientation.length; j++){
                                if(this.orientation[j] == this.selectedOrientation){
                                    this.finalOrientation = this.orientation[j-1];
                                    if(this.finalOrientation == undefined){
                                        this.finalOrientation = this.orientation[3];
                                    }
                                    this.rotationNumber -= 90
                                    break;
                                }
                            }
                        }else{
                            for(j = 0; j < this.orientation.length; j++){
                                if(this.orientation[j] == this.finalOrientation){
                                    this.finalOrientation = this.orientation[j-1];
                                    if(this.finalOrientation == undefined){
                                        this.finalOrientation = this.orientation[3];
                                    }
                                    this.rotationNumber -= 90
                                    break;
                                }
                            }  
                        }
                        if(this.finalCoordX == Number){
                            this.finalCoordX = parseInt(this.coordX)
                        }
                        if(this.finalCoordY == Number){
                            this.finalCoordY = parseInt(this.coordY)
                        }
                        this.showFinalCoordinates= true
                    }else if(this.instructions[i] == "A"){
                        if(this.finalOrientation == ""){
                            this.checkCoordinates(this.selectedOrientation)
                        }else{
                            this.checkCoordinates(this.finalOrientation)    
                        }
                    }else if(this.instructions[i] == "R"){
                        let k;
                        if(this.finalOrientation == ""){
                            for(k = 0; k < this.orientation.length; k++){
                                if(this.orientation[k] == this.selectedOrientation){
                                    this.finalOrientation = this.orientation[k+1];
                                    if(this.finalOrientation == undefined){
                                        this.finalOrientation = this.orientation[0];
                                    }
                                    this.rotationNumber += 90
                                    break;
                                }
                            }
                        }else{
                            for(k = 0; k < this.orientation.length; k++){
                                if(this.orientation[k] == this.finalOrientation){
                                    this.finalOrientation = this.orientation[k+1];
                                    if(this.finalOrientation == undefined){
                                        this.finalOrientation = this.orientation[0];
                                    }
                                    this.rotationNumber += 90
                                    break;
                                }
                            }
                        }

                        if(this.finalCoordX == Number){
                            this.finalCoordX = parseInt(this.coordX)
                        }
                        if(this.finalCoordY == Number){
                            this.finalCoordY = parseInt(this.coordY)
                        }
                        this.showFinalCoordinates= true  
                    }
                }
            }
            //To reset the initial data
            this.widthSquare = Number;
            this.heightSquare = Number;
            this.coordX = Number;
            this.coordY = Number;
            this.instructions = [];
            this.selectedOrientation = "N";
            setTimeout(()=>{ this.finalCoordX = Number; this.finalCoordY = Number; this.showFinalCoordinates = false;
            this.finalOrientation= ""}, 10000);
        }else{
            this.inputError = "All fields must be filled in"
        }    
    },


    checkCoordinates(orientation){
        switch(orientation){
            case "N":
                if(this.finalCoordY == Number){
                    this.finalCoordY = parseInt(this.coordY) + 1;
                    this.showFinalCoordinates = true
                    if(this.finalCoordY >= this.heightSquare){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }else{
                    this.finalCoordY++;
                    this.showFinalCoordinates = true;
                    if(this.finalCoordY >= this.heightSquare){
                        this.roverError = "The rover went out of limit";
                        this.showRoverError = true;
                        this.showFinalCoordinates = false;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }  
                if(this.finalCoordX == Number){
                    this.finalCoordX = parseInt(this.coordX)
                }

            break;
            case "E":
                if(this.finalCoordX == Number){
                    this.finalCoordX = parseInt(this.coordX) + 1;
                    this.showFinalCoordinates = true;
                    if(this.finalCoordX >= this.widthSquare){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }else{
                    this.finalCoordX++;
                    this.showFinalCoordinates = true;
                    if(this.finalCoordX >= this.widthSquare){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }
                if(this.finalCoordY == Number){
                    this.finalCoordY = parseInt(this.coordY)
                }
            break;
            case "S":
                if(this.finalCoordY == Number){
                    this.finalCoordY = parseInt(this.coordY) - 1;
                    this.showFinalCoordinates = true
                    if(this.finalCoordY <= -1){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }else{
                    this.finalCoordY--;
                    this.showFinalCoordinates = true;
                    if(this.finalCoordY <= -1){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    } 
                }

                if(this.finalCoordX == Number){
                    this.finalCoordX = parseInt(this.coordX)
                }
            break;
            case "W":
                if(this.finalCoordX == Number){
                    this.finalCoordX = parseInt(this.coordY) - 1;
                    this.showFinalCoordinates = true
                    if(this.finalCoordX <= -1){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    }
                }else{
                    this.finalCoordX--;
                    this.showFinalCoordinates = true;
                    if(this.finalCoordX <= -1){
                        this.roverError = "The rover went out of limit";
                        this.showFinalCoordinates = false;
                        this.showRoverError = true;
                        setTimeout(()=>{ this.showRoverError = false;}, 5000);
                    } 
                }
                if(this.finalCoordY == Number){
                    this.finalCoordY = parseInt(this.coordY)
                }
            break;
        }
    }  
  }
  
}
</script>


<style scope>
h5{
    font-weight: bold;
    margin-top: 10px
}

.commands{
    display:flex;
    flex-direction: column;
}

.closeModal{
    display: none
}

.finalResult{
    margin-top: 10px
}

.squareDiv{
    margin: auto;
    display:flex;
    justify-content: center
}

.square{
    background: #11324D;
    margin-bottom: 20px;
    display:flex;
    justify-content: flex-start;
    align-items: flex-end;
    
}

.roverSize{
    width: 50px;
}


/*ROVER ROTATIONS*/
.leftRotation{
    transform: rotate(-90deg)
}

.rightRotation{
    transform: rotate(90deg)
}
</style>