## How to open project and view the blueprint
    The read me file for camera algorism is in the comments in the algorism blueprints file
1. **assetfolder** is for camera algorism
2. **AssetLibrary** is for crowd simulation
### Load the whole project
1. Firstly, download the whole project. Copy or cut it into the project folder and the project should be seen in the unreal engine.
  ![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/3.png)
3. To view the crowd simulation blueprint, go to the Content folder -> AssetLibrary -> 4_BPTool -> Spline_ManFlow, double click the Spline_ManFlow( the ball ), and the blueprint window should be shown in the screen. The camera blueprint shold be opened in the same way.  
   ![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/4.png)
5. Switch between levels: We created three scenarios and it's in the **scene folder** which has 3 buttons, the broadcast room and locker room is easy for GPU to load, the stadium is hard for GPU.
   ![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/5.png)
### Access code base only
Both camera algorithm and crowd sim can run without the project. You can simplely create a new project in your unreal engine and import the two folders to your Content folder.
## Crowd simulation
### How to use it
Drag the crowd sim blueprint in the screen. You will see a man on the screen. If we want to longer the crowd line: click the white ball while press Alt, drag the line and there will be more people shown on the screen.
### Some constant (repeated to use or use to store the information) 
1. Skeletal Mesh Array: It stores all the information(keys and values ) of all the characters, which will be repeatedly used by the function below. 
2. `Get Spline Length()`: which is used to calculate the length of the crowd spline.  
![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/1.png)
![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/2.png) 
### Event Graph
1. `EventBeginPlay`: It uses the index of each model to load the animation to every character, also provide the way to change the animation.
2. `EventTick`: is similar to the main function and run the `Play()`, which will be explained below. 
### Construction Script
`Construction Script` is the tool which privide the real time viewport. It also provides the users options for one side crowd or two side crowd. One side crowd uses `AddLine()` once, two side crowd uses `AddLine()` twice, one of which reverse the direction. `AddLine()` will also be explained below.
### Play( )
1. To see the main process order, we need to follow the white line. Firstly, it use the keys and values stored in the Skeletal Mesh Array and execute in the `For Each Loop()`, which ensures every character is applied to the function. `Line Trace By Channel()` passes the output to the `SetWord Transform()`.
2. `Print String()`: It is followed by `For Each Loop()` to print the output to see the calculation result or character index for checking. It can also be connected to other function to check the output.
3. `Line Trace By Channel()`: It takes input of character location in x,y,z. How to calculate the position will be explained later. The vector Z( vertical direction) is minus 10000, provides the crwod characters to walk on the ground( in case the crowd line is not perfectly placed on the ground). `Line Trace By Channel()` passes the output to a `Select()` for users to choose whether to enable the Run on the ground feature. And then the output will be set to `SetWorld Transform()`.
4. `SetWorld Transform()`: It is used to change every character's position. The yellow Add pin in front of the function, which holds second and third parameters do not affect the location input, because X and Y value in the `Make Rotatior()` is set to zero.
5. `Break Transform()`: It is follows after `Get Transform at Distance()`. `Break Transform()`breaks the calculated positon(how calculate is explained below) into location and rotation. The location information is feed to `Line Trace By Channel()` and the rotation is feed to `SetWorld Transform()`. The reason only update the vertical direction to the character is because, unlike car or items on the belt, human's x and y rotation do not need to change when walking on the stairs.
6. Calculate the position part: Now the remain part of the `Play()` is the left botton part. Firstly, let's go back to the beginning, we can see the parameter of 'Forwoard speed' and 'Reverse speed'. The two speeds control the character's direction( one side or two side). Secondly, use `Get Time Seconds()` to have time of every frame. **Distance(every frame) =  speed * time**. Then, for the positive direction of the crowd, the **total distance = distance_from_start + distance_every_frame**. For the reverse direction, the totall distance needs to add the whole Spline lenght, in other word, character in reverse direction start in the end of the Spline and walk in the reverse speed.
### AddLine()
Similar to read the `Play()`, we need to follow the main process order(the white line). `Add Line()` -> `For Loop()` -> `Add Skeletal Mesh Component()` -> `Set Skinned Assert and Update()` -> `Set World Transform()`. The rest of other functions are used to calculate the input for the next function, or provide option for users to switch model (eg. enable one side or two side). The explain will take a clockwise direction of the entire code base of `Add Line()`.
1. `For Loop()`: It works the same in the `Play()`, ensuring all the characters are applied to the function.
2. `Add Skeletal Mesh Component()`: It is used to load the framework to each character (all the characters have the same framework, but have different skins). Then it passes the framework-loaded model to `Set Skinned Asset and Update()`.
3.   `Set Skinned Asset and Update()`: It is used to load the skin to each character. Since we have 11 different skins, so we use a `Random Bool()` to generate random numbers to allow the function to randomly attach the skin to each character.
4. `Line Trace By Channel()`:It's the same in `Play()`.
5. `Set World Transform()`: It is also the same in `Play()`, using the input position information to place each character. It happens all the time during the running, so the characters look like constantly moving.
6. Calculation part: **Firstly** from beginning of `Add Line()`, we get the lenght of the crowd spline, divided it by the interval which user inputs. So we get the number of nodes of the spline. The number will be minus 1 to calculate the number of characters on the spline. The number later will be feed to `For Loop()` to become the index of each character. **Secondly**, get every character's **distance from the start point = interval * index_of_each_character**, which system will know the distance from the start point of each character. Then add an offset(user can choose whether to enable it). **Lastly**, we pass the distance information to `Get Transfrom at Distance Along Spline()`. And it will break the distance information into position and rotation, then pass the output to `Line Trace By Channel()` and `SetWorld Transform()`, these two function will update the charachters in the 3D model.

Overall, the right part of code in `Add Line()`,  `Line Trace By Channel()`, `Setworld Transform()` together with the rotation part work the same in `Play()`.
The main difference of `Add Line()` is that it divides the Spline section by section to located each character.
## UI system
### How to run the UI 
It's in the Content folder:double click it and run the play button.
![image](https://github.com/1COASTLINE1/Blueprints_code/blob/main/Screen%20shot/2.png) 

