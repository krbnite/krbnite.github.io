---
title: Intel at the Edge (Deploying an Edge App)
layout: post
tags: edge ai easi
---

In this lesson, we focus more on the operational aspects of deploying a
edge app: we dive deeper into analying video streams, partcicularly those
streaming from a web cam; we dig into the pre-trained landscape, learning
to stack models into complex, beautiful pipelines;  and we learn about
a low-resource telemetry transport protocol, MQTT, which we employ
to send image and video statistics to a server.


# OpenCV Basics
OpenCV is a highly-optimized computer vision library built in C++, but callable
from Python environments using the `cv2` API. 

For an Edge app, one of the most useful shorthands that OpenCV provides is the 
ability to capture video streams, particularly those coming from a web cam.  It
has many convenient image processing functions as well, including various
computer vision techniques (e.g., Canny edge detction) as well as machine learning
techniques (e.g., it has its own neural network capabilities, though this is
not its recommended use case).

Some useful functions for Edge apps include:
* `cv2.VideoCapture`
* `cv2.resize`
* `cv2.cvtColor`
* `cv2.rectangle`
* `cv2.imwrite`


# Exercise
* https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_gui/py_video_display/py_video_display.html#display-video
* https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_canny/py_canny.html#canny
* https://www.learnopencv.com/read-write-and-display-a-video-using-opencv-cpp-python/

```python
import argparse
import cv2
import numpy as np
import os

def get_args():
    '''
    Gets the arguments from the command line.
    '''
    parser = argparse.ArgumentParser("Handle an input stream")
    # -- Create the descriptions for the commands
    i_desc = "The location of the input file"

    # -- Create the arguments
    parser.add_argument("-i", help=i_desc)
    args = parser.parse_args()

    return args


def capture_stream(args):
    ### TODO: Handle image, video or webcam
    if args.i[-3:] in ['jpg','bmp','png']:
        this_is_a_video=False
    elif args.i[-3:] in ['avi','mp4']:
        this_is_a_video=True
    else:
        raise ValueError('Must be image (ext: jpg, bmp, pn) or video (ext: avi, mp4).')

    ### TODO: Get and open video capture
    cap = cv2.VideoCapture(args.i)
    # I don't think cap.open is actually necessary...
    
    if this_is_a_video:
        # Create a video writer for the output video
        # The second argument should be cv2.VideoWriter_fourcc('M','J','P','G')`
        # on Mac, and `0x00000021` on Linux
        opsys = os.uname().sysname.lower()
        if opsys == 'linux':
            vw_arg2 = 0x00000021
        elif opsys == 'darwin':
            vw_arg2 = cv2.VideoWriter_fourcc('M','J','P','G')
        else:
            raise OSError("Not sure what OS this is.")
        out = cv2.VideoWriter('processed_'+args.i, vw_arg2, 30, (100,100))


    while cap.isOpened():
        still_processing, frame = cap.read()
        if not still_processing:
            break
        if cv2.waitKey(10) == ord('q'):
            print('User requests termination...')
            break
        
        ### TODO: Re-size the frame to 100x100
        frame = cv2.resize(frame,(100,100))

        ### TODO: Add Canny Edge Detection to the frame, 
        ###       with min & max values of 100 and 200
        ###       Make sure to use np.dstack after to make a 3-channel image
        frame = cv2.Canny(frame,100,200)
        frame = np.dstack((frame, frame, frame))

        ### TODO: Write out the frame, depending on image or video
        if this_is_a_video:
            out.write(frame)
        else:
            cv2.imwrite('processed_'+args.i, frame)

    ### TODO: Close the stream and any windows at the end of the application
    if this_is_a_video:
        out.release()
    cap.release()
    cv2.destroyAllWindows()

    
def main():
    args = get_args()
    capture_stream(args)


if __name__ == "__main__":
    main()
```

* [Official OpenCV-Python Tutorials](https://docs.opencv.org/master/d6/d00/tutorial_py_root.html)
* [Video Streaming in the Jupyter Notebook](https://towardsdatascience.com/video-streaming-in-the-jupyter-notebook-635bc5809e85)
* Maarten Breddels: [ipywebrtc examples](https://hub.gke.mybinder.org/user/maartenbreddels-ipywebrtc-pv0g5riu/tree/docs/source)
  - e.g., [CameraStream](https://hub.gke.mybinder.org/user/maartenbreddels-ipywebrtc-pv0g5riu/notebooks/docs/source/CameraStream.ipynb)
  


Things to get back to:
* https://software.intel.com/en-us/articles/get-started-with-neural-compute-stick
* http://www.jwrr.com/ncs2-2/
* https://docs.openvinotoolkit.org/2019_R3.1/_docs_install_guides_installing_openvino_docker_linux.html
