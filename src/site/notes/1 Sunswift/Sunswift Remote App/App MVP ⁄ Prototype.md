---
{"dg-publish":true,"permalink":"/1-sunswift/sunswift-remote-app/app-mvp-prototype/"}
---

### MVP Features
- Vehicle information:
	- Speed
	- Odometer
	- Vehicle state (drive mode)
	- Battery information (voltage, cell temperature etc.)
		- Charge stats similar to battery life graph on phones
	- Motor temperature
- GPS information: 
	- Vehicle location on GPS (integrate with google maps or something)
	- Share location to Google maps, distance to vehicle by foot
- Livestream footage:
	- Have access to camera feeds on the vehicle
- Car renders:
	- Either 3d renders or some vehicle pictures-> possible animations when the vehicle is moving
	- [Good reddit thread](https://www.reddit.com/r/webdev/comments/cvi7sn/i_want_to_implement_3d_modeling_into_an_appwebapp/)
	- Babylon.js or three.js
		- Connect these frameworks to react?
	- [Implement three.js with React Native](https://www.notjust.dev/blog/2023-02-17-3d-animations-in-react-native-with-threejs-nike-app)
- Frameworks: either flutter or react native
	- Flutter looks good to develop on screens with various sizes, issue is that it's a different programming language, and doesn't have much support for libraries such as three.js
	- React native looks good because of library support, also pre-existing familiarity with react/js

### Notes
- Worth reading: https://expo.dev/blog/from-web-to-native-with-react
- Experiment with React Native -> just run the emulator on powershell bro, wsl is not worth - have a separate installation on windows alongside wsl just for app dev
	- Get a CAD file/render of the sunswift car, and try to render it using three.js
		- [3D model showcase with React, Three.js and React Three Fiber](https://youtu.be/QaRIHrRclVk?si=mGwoq4WapPM8yPgD)
		- https://chatgpt.com/share/827cb4eb-00bb-44c2-a5df-ff1e19987001
	- Experiment with tailwind/random frameworks for data visualisation
	- Look into experimenting with Google maps API
	 ![Pasted image 20240708181300.png|possible location for vehicle models/renders - ask sander](/img/user/Images%20&%20Attachments/Pasted%20image%2020240708181300.png)
- Getting data from backend
	- Need to see what data can be taken from Grafana -> maybe make some routing for backend or somehow connect the app to the influxDB or whatever the data on the vehicle is stored on
	- From there, seeing what libraries can be used to visualise data
- Need to run `npx expo start` using powershell otherwise hot reload will not work 
- fucking dependencies man
- [Swift Connect](onenote:https://unsw.sharepoint.com/sites/SunswiftVIP-ChallENG/SiteAssets/Sunswift%20VIP%20-%20ChallENG%20Notebook/_Collaboration%20Space/Swift%20Connect.one#section-id={8DECD31E-2970-44CD-B337-043452071B7B}&end)  ([GitHub](https://github.com/UNSW-Sunswift/MCTL-2021-Swift-Connect))
	- ==Past sunswift app created in the past used to get information from the vehicle==
- Idea is that the main screen could look something like this, similar to tesla app:
	![Screenshot_1721194520 1.png|3d model of vehicle at top, with cards showing info beneath](/img/user/Images%20&%20Attachments/Screenshot_1721194520%201.png)
- Can message Sander about components/obj/models/renders from blender

### Issues Log
- Fucking dependencies errors -> dependency conflicts between `expo-three` and `three`, no one on the internet seems to have a clue on how to fix it
	- Fix: Just add `--legacy-peer-deps` after everything to deal with it for now
		- E.g. `npm i --legacy-peer-deps`
- `npm error Cannot read properties of null (reading 'children')`
	- idfk, people say you can just downgrade node to get around it
	- https://stackoverflow.com/questions/73356208/npm-err-cannot-read-properties-of-null-reading-children
- Errors with rendering `.obj` and `.glb` files with React Native, ranging from errors with the various loaders etc. 
	- Lots of people have the same issues with React Native, seems to work with standard React but not Native
	- Fix: WIP
		- Tried changing the `metro.config.js` file but still running into issues - see below
- `Unable to resolve "missing-asset-registry-path" from "node_modules\expo-router\assets\file.png"`
	- This was as a result of changing the `metro.config.js` file to try and work with loading `.obj` model files 
	- Fix: WIP
		- People suggesting different changes to the `metro.config.js` file but that conflicts with the changes to the file for loading `.obj`
- If `orbitControls` stops working, just restart expo go
- Sometimes expo go doesn't render/bundle the app properly when running -> network issue also need to make sure both smartphone and terminal are running on the same network
	- Use eduroam at uni
- Permissions error: Just cold boot/restart the app
- `OBJLoader` doesn't seem to work to render models, convert to `.stl` and use `STLLoader`
- `React-Three/drei` has really buggy/shitty imports that don't seem to work with React Native
	- `useTexture` and `useHelper` don't seem to work
	- `document.getElementByTagName is not a function (it is undefined)` -> most likely because it is not React and thus, no HTML/DOM manipulation allowed
![Pasted image 20240719140522.png|three.js loader's aren't the best because they're based on web stuff](/img/user/Images%20&%20Attachments/Pasted%20image%2020240719140522.png)
- LottieLoader errors, just reset expo and its cache
	- `expo start -c`
- Permissions error when running `npx expo start`
	- [Fix](https://stackoverflow.com/questions/33419474/node-fs-error-eperm-operation-not-permitted-open)
	- Uncheck the read-only property
	![Pasted image 20240722160459.png](/img/user/Images%20&%20Attachments/Pasted%20image%2020240722160459.png)
![Pasted image 20240722155846.png|stupid fucking permissions error](/img/user/Images%20&%20Attachments/Pasted%20image%2020240722155846.png)

### Progress Log
- 16/7: Finally got the fucking model to render, decided to switch from `.glb` to `.stl` and managed to get more luck that way
	- a lot of troubleshooting and finding out that a lot of react libraries/packages are just not compatible in react-native, especially for the 3d rendering stuff
	- also ran into issues where the model was just too high detail and wasn't able to be rendered so had to reduce triangle count:
		- https://lowpoly3d.xyz/
		- [some random codeSandbox link](https://codesandbox.io/s/react-render-stl-micr37?file=/src/App.tsx:787-837) -> didn't help cause it's for react not react native
	- currently using this library to control the camera -> it ports `three.js` orbit controls into react native: https://github.com/EvanBacon/expo-three-orbit-controls
	- also A LOT of libraries/packages are installed using `--legacy-peer-deps` because of conflicting packages between react and react-native versions
- 17/7: Implemented axes helper, and managed to render a plane/surface and a tyre
	- Idea to change tyres to accept position so that I can create multiple of them and specify their positioning -> group them together and have them rotate all individually when specified
![Pasted image 20240717153848.png](/img/user/Images%20&%20Attachments/Pasted%20image%2020240717153848.png)
- 19/7: Managed to render wheels on the vehicle, and also apply road texture to a platform via an image url -> show car moving on a track
	- Implemented basic wind streaks to show car is driving, but current bugs in how the wind streaks keep spawning and despawning at random intervals
![Pasted image 20240719220817.png](/img/user/Images%20&%20Attachments/Pasted%20image%2020240719220817.png)