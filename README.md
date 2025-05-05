# Real-Time Pose Estimation with Custom Graphics Overlay

This project leverages the **PoseNet** model from `ml5.js` for real-time pose estimation using the webcam feed. The goal of this project is to capture the user's movements through the webcam and overlay custom images (such as a character's face, glasses, and a cigar) based on specific body landmarks, such as the nose and eyes.

### Project Features:
- **Real-time webcam capture** to track the user's body movements.
- **PoseNet** model from `ml5.js` is used to estimate the user's pose.
- Custom images (like **glasses** and **cigar**) are dynamically positioned on the user's face based on detected pose keypoints.
- **Skeleton** drawing for visualizing the body pose.

---

### Libraries Used:
- **ml5.js**: A friendly machine learning library that makes machine learning accessible in JavaScript, used here for PoseNet model integration.
- **p5.js**: A JavaScript library for creative coding that simplifies drawing and animation.

---

### How It Works:

1. **Setup Webcam Feed**:
   - The webcam feed is captured using `createCapture(VIDEO)`, and it's hidden to only use the video stream as input.
   
2. **PoseNet Model Initialization**:
   - The `poseNet` model from `ml5.js` is loaded and applied to the webcam feed.
   - Once the model is loaded, it starts receiving pose predictions in real-time.

3. **Pose Detection**:
   - `receivedPoses` function is triggered whenever PoseNet detects a pose, extracting keypoints like the nose and eyes.
   
4. **Overlaying Graphics**:
   - Based on the detected keypoints (e.g., `nose`, `left eye`, `right eye`), custom images (glasses and cigar) are drawn over the user’s face.
   
5. **Skeleton Drawing**:
   - A skeleton is drawn by connecting keypoints to show the pose structure.

---

### Code Walkthrough:

```javascript
let capture;
let posenet;
let noseX, noseY;
let reyeX, reyeY;
let leyeX, leyeY;
let singlePose, skeleton;
let actor_img;
let specs, smoke;

function setup() {
    createCanvas(800, 500);
    capture = createCapture(VIDEO);
    capture.hide();

    posenet = ml5.poseNet(capture, modelLoaded);
    posenet.on('pose', receivedPoses);

    actor_img = loadImage('images/shahrukh.png'); // Custom actor image
    specs = loadImage('images/spects.png'); // Custom glasses image
    smoke = loadImage('images/cigar.png'); // Custom cigar image
}

function receivedPoses(poses) {
    console.log(poses);
    if (poses.length > 0) {
        singlePose = poses[0].pose;
        skeleton = poses[0].skeleton;
    }
}

function modelLoaded() {
    console.log('Model has loaded');
}

function draw() {
    // Draw the webcam feed to the canvas
    image(capture, 0, 0);
    
    fill(255, 0, 0);

    if (singlePose) {
        // Draw keypoints
        for (let i = 0; i < singlePose.keypoints.length; i++) {
            ellipse(singlePose.keypoints[i].position.x, singlePose.keypoints[i].position.y, 20);
        }
        
        // Draw skeleton
        stroke(255, 255, 255);
        strokeWeight(5);
        for (let j = 0; j < skeleton.length; j++) {
            line(skeleton[j][0].position.x, skeleton[j][0].position.y, skeleton[j][1].position.x, skeleton[j][1].position.y);
        }

        // Optional: Overlay custom images at keypoints
        // image(specs, singlePose.nose.x - 35, singlePose.nose.y - 50, 80, 80); // Glasses
        // image(smoke, singlePose.nose.x - 35, singlePose.nose.y + 10, 40, 40); // Cigar
    }
}
