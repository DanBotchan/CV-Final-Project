# Snooker Table Analysis & Ball Tracking

**Computer Vision Final Project (Winter 2020)**
*By Dan Botchan & Stav Yosef*

## 📝 Abstract
This project implements computer vision algorithms to analyze snooker gameplay from standard broadcast footage. By combining familiar CV algorithms with specific characteristics of a snooker table, we developed tools to:
1.  **Project the game state** into a Bird's Eye View (BEV).
2.  **Track and analyze** the movement and trajectory of the balls.

This creates a foundation for advanced sports analytics similar to Intel's 360 replay technology or VAR systems used in football.

---

## ⚙️ The Algorithm

Our pipeline relies on the assumption of a fixed camera angle and the distinct color of the snooker table.

### 1. Table Detection
We minimize the search area by isolating the table:
* **Color Space Conversion:** RGB to HSV (Hue is less sensitive to light intensity).
* **Masking:** Created a mask for green components.
* **Cropping:** Used Connected Components to find the largest area (the table) and cropped the frame.

### 2. Border & Intersection Detection
* **Lines:** We identify potential border points and use Linear Regression ($y=mx+c$) to average them into clean borderlines.
* **Vertices:** Intersections of these lines provide the 4 vertices of the table, which are crucial for the homography matrix.

### 3. Ball Detection
We detect balls based on prior knowledge that only balls or players appear on the table surface:
* **Canny Edge Detection** on the cropped table.
* **Dilation** to fill edges and reconstruct ball shapes.
* **Size Filtering** via Connected Components to differentiate balls from noise.
* **Classification:** We sampled RGB values of snooker balls to create a custom classifier. Each detected component is compared (using MSE) to known ball colors.

### 4. Tracking & BEV Projection
* **Tracking:** We match balls between frames using nearest-neighbor logic and color matching. This allows us to draw optical flow trails.
* **Homography:** Since the table is a planar surface, we use the 4 detected vertices to compute a projection matrix. This maps every pixel from the camera view to the 2D BEV plane.

---

## 🚀 Results

* **Video Demos:**
    * [Clip 1: Tracking Demo](https://youtu.be/p6EngngfAWk)
    * [Clip 2: Complex Shot Analysis](https://youtu.be/mX65D3GzrU)
    * [Clip 3: Multi-ball Break](https://youtu.be/V50s4olE44)

---

## 📚 Data Collection
Finding high-quality footage with both a static broadcast camera and a corresponding overhead view for validation was difficult.
* **Attempt 1:** Self-recording (Failed due to equipment/COVID restrictions).
* **Attempt 2:** Synthetic Data (Snooker 19 video game).
* **Final Solution:** We utilized professional footage from the *Sullivan vs. Taylor* match, which offered both broadcast and overhead replay angles for ground-truth comparison.

---

## 🔮 Future Work
* **3D Reconstruction:** Combine tracking with computer graphics for a full 3D replay.
* **Deep Learning:** Replace standard CV methods with CNNs for more robust ball detection in varying lighting.
* **Auto-Evaluation:** Create a metric to automatically compare our BEV result against the ground-truth overhead camera.

---

## 📖 References
1.  [El Clasico Broadcast Technology](https://scroll.in/field/954938/how-the-el-clasico-broadcast-is-special-in-spanish-football-a-peek-behind-the-scenes)
2.  Mathavan et al., *Application of high-speed imaging to determine the dynamics of billiards*.
3.  Denman et al., *Content Based Analysis for Video from Snooker Broadcasts*.
