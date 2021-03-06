# CAM_CAL_IPL

This is an implementation of manual camera calibration with EDA optimization.

`CAM_CAL_IPL` is joint work with Zheng (Thomas) Tang, Gaoang Wang, Hao Xiao, Aotian Zheng, and Prof. Jenq-Neng Hwang from the Information Processing Lab in the Department of Electrical Engineering, University of Washington. 

## How It Works

From each camera view, we first manually label two pairs of vanishing lines, i.e., parallel line pairs on the 3D ground plane that are orthogonal with each other, from which we can derive the two vanishing points on the ground plane, noted _V_X_ and _V_Y_. It has been proven that all the camera parameters in a 3�4 projection matrix **P** can be computed from _V_X_ and _V_Y_ with some constraints on intrinsic camera parameters. To relax these constraints for more accurate estimation of camera parameters, we formulate an optimization problem to minimize the reprojection error. A set of line segments on the ground plane, each defined by two endpoints are manually selected, whose ground-truth 3D lengths are measured in the Google Maps. Using the calculated camera parameters, the 2D endpoints of the line segments can be back projected to 3D. Their Euclidean distances represent the estimated 3D lengths of the line segments. The absolute differences between estimations and ground truths are summed up to describe the reprojection error. Thus, our objective is to minimize the reprojection error.

The non-linear optimization problem can be iteratively solved by the Estimation of Distribution Algorithm (EDA), which is a classic evolutionary algorithm. At the first iteration of EDA, an initial population of camera parameters, noted _R_, is generated by uniform distribution. Among _R_, a selected population, noted _N_, with the lowest estimated reprojection error is chosen to fit a probability density function (pdf) with eleven-variate normal distribution, as there are 11 camera parameters in **P** to be optimized. Then, we move on to the next iteration to generate a new initial population and select the optimum subset to estimate the pdf again. This iterative process stops until the mean of the estimated pdf is smaller than a specified threshold. 

## Getting Started

### Prerequisites

0. Windows or Linux system
1. OpenCV (included as a 3rd-party software component in the package)

The code has been tested on Windows 10 64 bit with Visual Studio 2017 (v141). 

### Installing

Build the VC++ solution in Release mode in Visual Studio. The source code is under `.\src`. The 3rd-party software components (headers, libraries and/or DLLs) are included in `.\3rdparty`.

When running the solution, make sure that you have the configuration file `cfg.json` set properly under the `.\data\` folder. All the required input files and folders should be at their corresponding locations set in the configuration file. In the `.\data\` folder, an example of input and output is given. 

1. The user needs to provide an approximate range (ideally 0.5 to 1 meter) of camera height in `calCamHeiMax` and `calCamHeiMin`.
2. For manual calibration, if `calSelVanLnFlg` is not set, the user can manually input the 2D coordinates of two vanishing points Vr (on the right) and Vl (on the left) respectively in `calVr` and `calVl`. Otherwise, s/he can follow similar procedure as ROI selection to select two pairs of vanishing lines, i.e., parallel lines on the ground plane in 3D that are perpendicular to each other, in the window of �selector of vanishing lines�. Please select a pair of vanishing lines first and then another pair. There should be 8 points clicked in total. After the selection of vanishing lines is done, click `o`. Then, the window �3D grid on ground plane� will appear showing the update of calibration result. 
<div align="center">
    <img src="demo0.jpg", width="1000">
</div>
<div align="center">
    <img src="demo1.jpg", width="1000">
</div>
3.	When `calEdaOptFlg` is set, the camera parameters will be optimized by the estimation of distribution algorithm (EDA) to minimize reprojection error. The user can input the end points of line segments and their true 3D distances respectively in `calMeasLnSegNdPt` and `calMeasLnSegDist`. 


### Input/Output Format

For output camera parameters in text, the format is as follows:

\<K_0\> \<K_1\> \<K_2\> \<K_3\> \<K_4\> \<K_5\> \<K_6\> \<K_7\> \<K_8\>

\<R_0\> \<R_1\> \<R_2\> \<R_3\> \<R_4\> \<R_5\> \<R_6\> \<R_7\> \<R_8\>

\<t_0\> \<t_1\> \<t_2\>

\<P_0\> \<P_1\> \<P_2\> \<P_3\> \<P_4\> \<P_5\> \<P_6\> \<P_7\> \<P_8\> \<P_9\> \<P_10\> \<P_11\>

K, R, t and P respectively stand for the intrinsic camera matrix, the rotation matrix, the translation vector and the projection matrix. In the 3D coordinate system, the ground plane is equivalent to the X-Y plane. The Z axis points upward and passes through the camera location. 