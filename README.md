# Radar Target Generation and Detection

Project for Udacity's Sensor Fusion Engineer Nanodegree Program

### Project Goals

This is a MATLAB based Radar Target Genetation and Detection implementation for:
* FMCW waveform design using provided radar-design specifications
* moving target generation
* signal propagation
* received signal processing
* range-doppler FFT implementation
* CFAR detection

### Notes on implementation

The implementation was straightforward and following are some details/notes:

**1. Radar specificatins**

The specifications were pre-provided and are listed below:

```
%% Radar Specifications
%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Frequency of operation = 77GHz
% Max Range = 200m
% Range Resolution = 1 m
% Max Velocity = 100 m/s
```

**2. target specifications**

For target parameters I used following values:

```
%% User Defined Range and Velocity of target
initial-position = 100 m
initial-velocity = 25 m/s
```

**3. FMCW waveform generation**

standard bandwidth and chirp formulae were used for this purpose from [this link](/images/kdtree_sample.jpg). A sample transmit and receive signal slope is depicted in figure below:


chirp-slope:
![alt text](images/chirp_slope.jpg)

**4. FFT and Range-Doppler Map**

1D FFT along range grids and 2D fft along range-velocity grids were used to generated range FFT and range-velocity-map, as shown here:

range-FFT output:
![range_fft](images/range_fft.jpg)

range-doppler-map (RDM):
![rdm](images/range_doppler_map.jpg)


**5. CFAR implementation**

CFAR implementation steps were outlined in the started code provided, so modifications were made to it based on recommendations.

  **5.1: Implementation steps for the 2D CFAR process:**

```
%design a loop such that it slides the CUT across range doppler map by
%giving margins at the edges for Training and Guard Cells.
%For every iteration sum the signal level within all the training
%cells. To sum convert the value from logarithmic to linear using db2pow
%function. Average the summed values for all of the training
%cells used. After averaging convert it back to logarithimic using pow2db.
%Further add the offset to it to determine the threshold. Next, compare the
%signal under CUT with this threshold. If the CUT level > threshold assign
%it a value of 1, else equate it to 0.
```

  **5.2: Selection of Training, Guard cells and offset:**

Training and Guard cell values were randomly selected based on some past experience:

```
%Select the number of Training Cells in both the dimensions.
Tr = 8;
Td = 4;

%Select the number of Guard Cells in both dimensions
Gr = 4;
Gd = 2;
```

Offset value was taken as dB equivalent of maximum noise floor value present in RDM:

```
% offset the threshold by SNR value in dB
snr_offset = -10*log10(0.24253);
```


  **5.3: Steps taken to suppress the non-thresholded cells at the edges:**
  
  The CFAR-signal matrix was assigned dimensions of RDM and all values were initialized to zero, hence, there was no need to follow up with re-assignment or suppression of non-threshold cells around edges.
  
  The final CFAR output is shown below, with a peak and spread around it centered at 100m in range direction and 25m/s in the doppler direction.


![cfar](images/cfar_output.jpg)
