Computer Vision

in requirement.txt file

opencv-python
numpy

Practical 1: pip install opencv-python numpy matplotlib tensorflow scikit-learn tensorflow-datasets

import cv2
import numpy as np

print("OpenCV Version:", cv2.__version__)
print("OpenCV is installed and working successfully.")

# Create a simple sample image (black background with shapes).
image = np.zeros((400, 600, 3), dtype=np.uint8)
cv2.putText(image, "OpenCV Working", (90, 170),
            cv2.FONT_HERSHEY_SIMPLEX, 1.5, (255, 255, 255), 3)
cv2.circle(image, (300, 260), 60, (0, 255, 0), 3)
cv2.imshow("Practical 1 - OpenCV Test", image)
cv2.waitKey(0)
cv2.destroyAllWindows()

____________________________________________________________________________________________________

Practical 2: Basic Image Processing
Topics: Loading Images, Cropping, Resizing, Thresholding, Contour Analysis and Blob Detection.

import cv2
import numpy as np
import os
path = "input.png"
if os.path.exists(path):
    image = cv2.imread(path)
else:
    image = np.full((400, 600, 3), 255, dtype=np.uint8)
    cv2.circle(image, (150, 150), 55, (0, 0, 0), -1)
    cv2.rectangle(image, (280, 80), (420, 220), (0, 0, 0), -1)
    cv2.ellipse(image, (200, 310), (80, 35), 0, 0, 360, (0, 0, 0), -1)
    cv2.putText(image, "Sample Image", (350, 330),
                cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 0, 0), 2)
if image is None:
    raise FileNotFoundError("Could not read input.jpg. Check the image file.")
# 1. Loading
print("Image loaded successfully. Dimensions:", image.shape)
# 2. Resizing
resized = cv2.resize(image, (400, 300))
# 3. Cropping (central region)
h, w = image.shape[:2]
cropped = image[h//4:3*h//4, w//4:3*w//4]
# 4. Grayscale and thresholding
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
_, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
# 5. Contour analysis
contours, _ = cv2.findContours(255 - thresh, cv2.RETR_EXTERNAL,
                               cv2.CHAIN_APPROX_SIMPLE)
contour_image = image.copy()
cv2.drawContours(contour_image, contours, -1, (0, 0, 255), 2)
print("Number of contours detected:", len(contours))
# 6. Blob detection using SimpleBlobDetector
params = cv2.SimpleBlobDetector_Params()
params.filterByArea = True
params.minArea = 50
params.maxArea = 50000
params.filterByCircularity = False
params.filterByConvexity = False
params.filterByInertia = False
detector = cv2.SimpleBlobDetector_create(params)
keypoints = detector.detect(gray)
blob_image = cv2.drawKeypoints(image, keypoints, None, (0, 0, 255),
                               cv2.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS)
print("Number of blobs detected:", len(keypoints))
cv2.imshow("Original Image", image)
cv2.imshow("Resized Image", resized)
cv2.imshow("Cropped Image", cropped)
cv2.imshow("Threshold Image", thresh)
cv2.imshow("Contour Analysis", contour_image)
cv2.imshow("Blob Detection", blob_image)
print("Press any key in an image window to close all windows.")
cv2.waitKey(0)
cv2.destroyAllWindows()
____________________________________________________________________________________________________

Practical 3: Practical No: 3    Image Annotation – Drawing lines, text circle, rectangle, ellipse on images

import cv2
import numpy as np
import os
path = "input.jpg"
if os.path.exists(path):
    image = cv2.imread(path)
else:
    image = np.full((500, 700, 3), 255, dtype=np.uint8)
    cv2.putText(image, "Sample Image", (180, 250),
                cv2.FONT_HERSHEY_SIMPLEX, 1.3, (80, 80, 80), 3)
if image is None:
    raise FileNotFoundError("Could not read input.jpg. Check the image file.")
annotated = image.copy()
# Draw a line
cv2.line(annotated, (20, 30), (300, 30), (255, 0, 0), 3)
# Add text
cv2.putText(annotated, "OpenCV Annotation", (20, 75),
            cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 0, 255), 2)
# Draw a circle
cv2.circle(annotated, (100, 160), 45, (0, 255, 0), 3)
# Draw a rectangle
cv2.rectangle(annotated, (180, 115), (330, 205), (255, 0, 255), 3)
# Draw an ellipse
cv2.ellipse(annotated, (450, 160), (80, 40), 0, 0, 360, (0, 165, 255), 3)
cv2.imwrite("annotated_output.jpg", annotated)
print("Annotations added successfully.")
print("Saved output image as annotated_output.jpg")
cv2.imshow("Practical 3 - Image Annotation", annotated)
cv2.waitKey(0)
cv2.destroyAllWindows()
____________________________________________________________________________________________________

Deep Learning

in requirement.txt file
tensorflow
tensorflow-datasets
numpy
scikit-learn
matplotlib


Practical = 4  Linear regression using a Deep Neural Network. 

import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
def load_data():
    try:
        # Available in some TensorFlow/Keras versions.
        (x_train, y_train), (x_test, y_test) = tf.keras.datasets.boston_housing.load_data()
        print("Dataset: Boston Housing")
        return x_train, x_test, y_train, y_test
    except Exception as exc:
        print("Boston Housing unavailable; using California Housing instead.")
        print("Reason:", exc)
        data = fetch_california_housing()
        return train_test_split(data.data, data.target, test_size=0.2, random_state=42)
x_train, x_test, y_train, y_test = load_data()
scaler = StandardScaler()
x_train = scaler.fit_transform(x_train)
x_test = scaler.transform(x_test)
model = tf.keras.Sequential([
    tf.keras.layers.Input(shape=(x_train.shape[1],)),
    tf.keras.layers.Dense(64, activation="relu"),
    tf.keras.layers.Dense(32, activation="relu"),
    tf.keras.layers.Dense(1)
])
model.compile(optimizer="adam", loss="mse", metrics=["mae"])
history = model.fit(x_train, y_train, epochs=50, batch_size=16,
                    validation_split=0.2, verbose=1)
loss, mae = model.evaluate(x_test, y_test, verbose=0)
print(f"Test MSE: {loss:.4f}")
print(f"Test MAE: {mae:.4f}")
pred = model.predict(x_test, verbose=0).flatten()
plt.figure(figsize=(7, 5))
plt.scatter(y_test, pred, alpha=0.6)
low = min(float(np.min(y_test)), float(np.min(pred)))
high = max(float(np.max(y_test)), float(np.max(pred)))
plt.plot([low, high], [low, high], "r--")
plt.xlabel("Actual house value")
plt.ylabel("Predicted house value")
plt.title("DNN Regression: Actual vs Predicted")
plt.tight_layout()
plt.show()

____________________________________________________________________________________________________

Practical = 5 Multiclass classification using a Deep Neural Network.

"""
Practical 5: Multiclass classification using a Deep Neural Network.
Dataset: EMNIST Letters (handwritten A-Z images).
Output: test accuracy and a sample image with predicted letter.
"""
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
import tensorflow_datasets as tfds

IMG_SIZE = 28
BATCH_SIZE = 128

(ds_train, ds_test), info = tfds.load(
    "emnist/letters",
    split=["train", "test"],
    as_supervised=True,
    with_info=True
)

def prepare(image, label):
    image = tf.cast(image, tf.float32) / 255.0
    image = tf.reshape(image, (IMG_SIZE * IMG_SIZE,))
    # EMNIST Letters labels are 1..26; convert to 0..25.
    label = tf.cast(label, tf.int32) - 1
    return image, label

ds_train = ds_train.map(prepare, num_parallel_calls=tf.data.AUTOTUNE)
ds_test = ds_test.map(prepare, num_parallel_calls=tf.data.AUTOTUNE)
ds_train = ds_train.shuffle(10000).batch(BATCH_SIZE).prefetch(tf.data.AUTOTUNE)
ds_test = ds_test.batch(BATCH_SIZE).prefetch(tf.data.AUTOTUNE)

model = tf.keras.Sequential([
    tf.keras.layers.Input(shape=(784,)),
    tf.keras.layers.Dense(256, activation="relu"),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(128, activation="relu"),
    tf.keras.layers.Dense(26, activation="softmax")
])
model.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])
model.fit(ds_train, epochs=5, validation_data=ds_test)
loss, accuracy = model.evaluate(ds_test, verbose=0)
print(f"Test accuracy: {accuracy * 100:.2f}%")

for images, labels in ds_test.take(1):
    probabilities = model.predict(images, verbose=0)
    index = 0
    predicted = int(np.argmax(probabilities[index]))
    actual = int(labels[index].numpy())
    plt.imshow(images[index].numpy().reshape(28, 28), cmap="gray")
    plt.title(f"Actual: {chr(actual + 65)} | Predicted: {chr(predicted + 65)}")
    plt.axis("off")
    plt.show()
____________________________________________________________________________________________________

Practical =6 Image classification using CNN on Fashion-MNIST

import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf

class_names = [
    "T-shirt/top", "Trouser", "Pullover", "Dress", "Coat",
    "Sandal", "Shirt", "Sneaker", "Bag", "Ankle boot"
]
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.fashion_mnist.load_data()
x_train = x_train.astype("float32") / 255.0
x_test = x_test.astype("float32") / 255.0
x_train = x_train[..., np.newaxis]
x_test = x_test[..., np.newaxis]

model = tf.keras.Sequential([
    tf.keras.layers.Input(shape=(28, 28, 1)),
    tf.keras.layers.Conv2D(32, (3, 3), activation="relu"),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Conv2D(64, (3, 3), activation="relu"),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation="relu"),
    tf.keras.layers.Dropout(0.3),
    tf.keras.layers.Dense(10, activation="softmax")
])
model.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])
model.fit(x_train, y_train, epochs=5, batch_size=64,
          validation_split=0.1, verbose=1)
loss, accuracy = model.evaluate(x_test, y_test, verbose=0)
print(f"Test accuracy: {accuracy * 100:.2f}%")
print(f"Test loss: {loss:.4f}")

predictions = model.predict(x_test[:12], verbose=0)
plt.figure(figsize=(10, 5))
for i in range(10):
    plt.subplot(2, 5, i + 1)
    plt.imshow(x_test[i].squeeze(), cmap="gray")
    predicted = int(np.argmax(predictions[i]))
    plt.title(f"P: {class_names[predicted]}\\nA: {class_names[y_test[i]]}", fontsize=8)
    plt.axis("off")
plt.tight_layout()
plt.show()
____________________________________________________________________________________________________

