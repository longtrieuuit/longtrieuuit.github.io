---
title:  "Python How to clone image in opencv"
categories: python
permalink: Python_How_to_clone_image_in_opencv.html
tags: [opencv]
---

*  Install OpenCV with pip
```
pip install python-opencv
```

*  cv2 clone image sample source

	```python
	import cv2
	import sys

		if __name__ == '__main__':
			img = cv2.imread('test.jpg')
			clone_img = img.copy()

			cv2.putText(img, "original", (10, 10), cv2.FONT_HERSHEY_SIMPLEX ,1.2, (0, 0, 255), 2)
			cv2.putText(clone_img, "clone_img", (10, 10), cv2.FONT_HERSHEY_SIMPLEX ,1.2, (0, 0, 255), 2)
			cv2.imshow('original',img)
			cv2.imshow('clone',clone_img)
			cv2.waitKey(10000)
	```


{% include links.html %}
