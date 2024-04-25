# TraSA for FairMOT

* **A Single-Target Attack example (Attack ID: 19; Screener ID: 24):**

<table>
    <tr>
        <td ><center><img src="assets/original.gif" ><b> Fig.1  Original </b> </center></td>
        <td ><center><img src="assets/attacked.gif" ><b> Fig.2  Attacked </b> </center></td>
    </tr>
</table>
By perturbing only two frames in this example video, we can exchange the 19th ID and the 24th ID completely. Starting from frame 592, the 19th and 24th IDs can keep the exchange without noise.

**Related Works**

* [TraSA for ByteTrack](https://github.com/DerryHub/ByteTrack-attack)

## Abstract

hough achieving aggressive progress, there are only a few explorations on the robustness of Multi-Object Tracking (MOT) trackers. 
Most of the existing MOT researches focus on pedestrian tracking, yet there is little research on its adversarial attack, hindering the robustness improvement study on these systems. It is also challenging to attack these systems since various mature association algorithms have been designed to be robust against errors during the tracking. In this work, we analyze the vulnerability of typical pedestrian MOT trackers and propose a novel adversarial attack method called Tracklet-Switch Attack (TraSA) against the complete tracking pipeline. By perturbing very few frames, the proposed TraSA can spoof the advanced deep pedestrian trackers (i.e., FairMOT and ByteTrack), causing them fail to track the targets in subsequent frames. Specifically, TraSA learns an effective perturbation generator to make the tracker confuse intersecting trajectories by attacking very few frames, then keeps the error across frames to the end of the sequences without any more perturbation. In our method, two new losses are proposed: PushPull works on the re-identification (re-ID) branch to perturb two approaching pedestrian detection boxes, while CenterLeaping works on the detection branch to perturb pedestrian features to make their trajectories switch. Experiments on the MOT-Challenge datasets (i.e., 2DMOT15, MOT17, and MOT20) show that TraSA can achieve an extraordinarily high success attack rate of over 90% by attacking only four frames on average.

## Attack Performance

**Single-Target Attack Results on MOT challenge test set**

| Dataset | Suc. Rate | Avg. Frames | Total L<sub>2</sub> Distance |
| :-----: | :-------: | :---------: | :--------------------------: |
| 2DMOT15 |  93.28%   |    4.25     |            13.99             |
|  MOT17  |  91.03%   |    4.74     |            14.40             |
|  MOT20  |  96.46%   |    3.94     |            11.67             |

## Installation

* **same as** [FairMOT](https://github.com/microsoft/FairMOT)

* Clone this repo, and we'll call the directory that you cloned as ${FA_ROOT}

* Install dependencies. We use python 3.7 and pytorch >= 1.2.0

* ```shell
  conda create -n FA
  conda activate FA
  conda install pytorch==1.2.0 torchvision==0.4.0 cudatoolkit=10.0 -c pytorch
  cd ${FA_ROOT}
  pip install -r requirements.txt
  cd src/lib/models/networks/DCNv2 sh make.sh
  ```

* We use [DCNv2](https://github.com/CharlesShang/DCNv2) in our backbone network and more details can be found in their repo.

* In order to run the code for demos, you also need to install [ffmpeg](https://www.ffmpeg.org/).

## Data preparation

* We only use the same test data as [FairMOT](https://github.com/microsoft/FairMOT).

* 2DMOT15, MOT17 and MOT20 can be downloaded from the official webpage of [MOT-Challenge](https://motchallenge.net/). After downloading, you should prepare the data in the following structure:

  ```
  ${DATA_DIR}
      ├── MOT15
      │   └── images
      │       ├── test
      │       └── train
      ├── MOT17
      │   └── images
      │       ├── test
      │       └── train
      └── MOT20
          └── images
              ├── test
              └── train
  ```

## Target Model

* We choose DLA-34: [[Google]](https://drive.google.com/open?id=1udpOPum8fJdoEQm6n0jsIgMMViOMFinu) [[Baidu, code: 88yn]](https://pan.baidu.com/s/1YQGulGblw_hrfvwiO6MIvA) trained by [FairMOT](https://github.com/microsoft/FairMOT) as our primary target model.

## Tracking without Attack

* tracking on original videos of 2DMOT15, MOT17, and MOT20

```shell
cd src
python track.py mot --test_mot15 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR}
python track.py mot --test_mot17 True --load_model all_dla34.pth --conf_thres 0.4 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR}
python track.py mot --test_mot20 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR}
```

## Attack

* attack all attackable objects separately in videos in parallel (may require a lot of memory).

```shell
cd src
python track.py mot --test_mot15 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id -1
python track.py mot --test_mot17 True --load_model all_dla34.pth --conf_thres 0.4 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id -1
python track.py mot --test_mot20 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id -1
```

* attack a specific object in a specific video (require to set specific video in `src/track.py`).

```shell
cd src
python track.py mot --test_mot15 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id ${a specific id in origial tracklets}
python track.py mot --test_mot17 True --load_model all_dla34.pth --conf_thres 0.4 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id ${a specific id in origial tracklets}
python track.py mot --test_mot20 True --load_model all_dla34.pth --conf_thres 0.3 --data_dir ${DATA_DIR} --output_dir ${OUTPUT_DIR} --attack single --attack_id ${a specific id in origial tracklets}
```

## Acknowledgement

This source code is based on [FairMOT](https://github.com/microsoft/FairMOT). Thanks for their wonderful works.

## Citation

```
@misc{lin2021trasw,
      title={Tracklet-Switch Adversarial Attacks against Multi-Object Tracking}, 
      author={Delv Lin and Qi Chen and Chengyu Zhou and Kun He},
      year={2021},
      eprint={2111.08954},
      archivePrefix={arXiv},
      primaryClass={cs.CV}
}
```

