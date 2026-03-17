# GSoC 2026 — Project 3.3: Integrating Planter into P4Pi

**Applicant:** Takumi Ishihara ([@takuan517](https://github.com/takuan517))  
**Organization:** [P4 Language Consortium](https://p4.org)  
**Project:** [Project 3.3 — Integrating P4-based In-Network ML (Planter) into P4Pi](https://github.com/p4lang/gsoc)  
**Mentor:** Peng Qian (University of Oxford)

---

## Overview

This repository documents my qualification task and application materials for GSoC 2026 Project 3.3.

The project goal is to integrate [Planter](https://github.com/In-Network-Machine-Learning/Planter) — a modular in-network machine learning framework — into [P4Pi](https://github.com/p4lang/p4pi) by adding a new `src/targets/dpdk/` backend that compiles P4 programs via `p4c-dpdk` and runs them using `bf_switchd` on Raspberry Pi.

---

## Qualification Task

The qualification task for Project 3.3 is to run the full Planter end-to-end workflow on BMv2 and report the three accuracy matrices.

### What the task involves

Planter encodes a trained ML model into P4 match-action tables and deploys it onto a P4 switch. The workflow has three validation stages:

| Matrix | What it tests |
|--------|--------------|
| **Matrix 1** | sklearn model accuracy — pure Python, no P4 involved |
| **Matrix 2** | Table logic simulation — ML model → P4 match-action tables, validated in software |
| **Matrix 3** | BMv2 switch deployment — P4 tables loaded onto a Mininet software switch via P4Runtime |

All three matrices must match, confirming that the model is correctly translated from sklearn → P4 tables → live switch packet classification.

### Configuration

| Parameter | Value |
|-----------|-------|
| Model | Random Forest / Type EB |
| Dataset | Iris (4 features, 3 classes) |
| Trees | 5 |
| Max depth | 4 |
| Leaf nodes | 1000 |
| P4 architecture | v1model |
| Use case | performance |
| Target | BMv2 software |
| Runtime | Mininet + simple_switch_grpc + P4Runtime gRPC |

### Environment

| Component | Details |
|-----------|---------|
| Machine | Apple Silicon M5 (Mac) |
| VM | UTM (ARM virtualization) |
| OS | Ubuntu 24.04 LTS ARM64 |
| Python | 3.12 |
| p4c | v1.2+ |
| Planter | main branch |

---

### Results

| Matrix | Description | Accuracy |
|--------|-------------|----------|
| Matrix 1 | sklearn Random Forest on test set | **0.9556** |
| Matrix 2 | P4 table logic simulation | **0.9556** |
| Matrix 3 | BMv2 switch (live packet classification) | **0.9556** ✔ |

All three matrices match. The RF model is correctly encoded into P4 match-action tables and executes accurately on a software switch.

### Screenshots

#### Matrix 1 — sklearn accuracy
![Matrix 1](screenshots/matrix1_sklearn.png)

#### Matrix 2 — P4 table logic simulation
![Matrix 2](screenshots/matrix2_table.png)

#### Matrix 3 — BMv2 switch deployment + Test Finished
![Matrix 3](screenshots/matrix3_bmv2.png)

---

### Full execution log

<details>
<summary>Click to expand — <code>python3 Planter.py -m</code> full output</summary>

```
(p4dev-python-venv) takuan@ubuntu1:~/Planter$ python3 Planter.py -m
 ____    ___                    __ 
/\  _`\ /\_ \                  /\ \__ 
\ \ \L\ \//\ \      __      ___\ \ ,_\    __  _ __ 
 \ \ ,__/ \ \ \   /'__`\  /' _ `\ \ \/  /'__`/\`'__\ 
  \ \ \/   \_\ \_/\ \L\.\_/\ \/\ \ \ \_/\  __\ \ \/ 
   \ \_\   /\____\ \__/.\_\ \_\ \_\ \__\ \____\ \_\ 
    \/_/   \/____/\/__/\/_/\/_/\/_/\/__/\/____/\/_/ 
                                      Version 0.1.0

Please set the following configurations:
+ Where is your data folder? (default = /home/takuan/Data) 
+ Where is your Planter folder? (default = /home/takuan/Planter) 
+ Which model do you want to plant? (default = DT | options = -h) RF
+ Which type (variation) of model do you want to plant? (default = 1 | options = -h) EB
+ Which dataset do you want to use? (default = UNSW_5_tuple | options = -h) Iris
+ Where is the number of features? (default = 4) 4
= src/configs/Planter_config.json is generated
+ Use the testing mode or not? (default = y) n
= The shape of the dataset -  train x:  (105, 4) train y:  (105,) test x:  (45, 4) test y:  (45,)
= src/configs/Planter_config.json is generated
= Add the following path: /home/takuan/Planter/src/models/RF/Type_EB
- Number of trees? (default = 5) 5
- Number of depth? (default = 4) 4
- Number of leaf nodes? (default = 1000) 1000

               precision    recall  f1-score   support

           0     1.0000    1.0000    1.0000        13
           1     0.9500    0.9500    0.9500        20
           2     0.9167    0.9167    0.9167        12

    accuracy                         0.9556        45
   macro avg     0.9556    0.9556    0.9556        45
weighted avg     0.9556    0.9556    0.9556        45

Classification results are downloaded to log as src/logs/log.json
The table for Tree: 4 is generated
Generating vote to class table...Done
Begine transfer: Feature table 0
Input table has:  80 entries and:  6  different ranges, range match with default:  4 , and output TCAM entry has 13
range match with default:  4
80th testing sample with correct matches: 100.0 % and 0 errors. 
Begine transfer: Feature table 1
Input table has:  45 entries and:  5  different ranges, range match with default:  3 , and output TCAM entry has 12
range match with default:  3
45th testing sample with correct matches: 100.0 % and 0 errors. 
Begine transfer: Feature table 2
Input table has:  70 entries and:  8  different ranges, range match with default:  6 , and output TCAM entry has 18
range match with default:  6
70th testing sample with correct matches: 100.0 % and 0 errors. 
Begine transfer: Feature table 3
Input table has:  26 entries and:  7  different ranges, range match with default:  6 , and output TCAM entry has 11
range match with default:  6
26th testing sample with correct matches: 100.0 % and 0 errors. 
Ternary_Table is generated
Exact_Table is generated
/home/takuan/Planter/src/configs/Planter_config.json is generated
+ Test the table or not? (default = y) y
Test the exact feature table, extact code and decision table (feel free if the acc to sklearn is slightly lower than 1)
switch_prediction: 0, test_y: 0, with acc: 1.0, with acc to sklearn: 1.0, with error: 0.0,switch_prediction: 0, test_y: 0, with acc: 1.0, with acc to sklearn: 1.0, with error: 0.0,switch_prediction: 2, test_y: 2, with acc: 1.0, with acc to sklearn: 1.0, with error: 0.0,switch_prediction: 1, test_y: 1, with acc: 1.0, with acc to sklearn: 1.0, with error: 0.0,switch_prediction: 1, test_y: 2, with acc: 0.833, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.857, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.875, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.889, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.9, with acc to sklearn: 1.0, with error: 0.0,switch_prediction: 2, test_y: 2, with acc: 0.909, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.917, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.923, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.929, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.933, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.938, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.941, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.944, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.947, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.95, with acc to sklearn: 1.0, with error: 0.0switch_prediction: 1, test_y: 1, with acc: 0.952, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.955, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.957, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.958, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.96, with acc to sklearn: 1.0, with error: 0.0switch_prediction: 1, test_y: 1, with acc: 0.962, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.963, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.964, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.966, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.967, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.968, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.969, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.97, with acc to sklearn: 1.0, with error: 0.0switch_prediction: 1, test_y: 1, with acc: 0.971, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.971, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.972, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.973, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.974, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.974, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 2, with acc: 0.975, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.976, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.976, with acc to sklearn: 1.0, with error: 0.switch_prediction: 0, test_y: 0, with acc: 0.977, with acc to sklearn: 1.0, with error: 0.switch_prediction: 2, test_y: 1, with acc: 0.955, with acc to sklearn: 1.0, with error: 0.switch_prediction: 1, test_y: 1, with acc: 0.956, with acc to sklearn: 1.0, with error: 0.0, M/A format macro f1: 0.955, macro f1: 0.955 
The accuracy of the match action format of Random Forest is 0.9555555555555556

               precision    recall  f1-score   support

           0     1.0000    1.0000    1.0000        13
           1     0.9500    0.9500    0.9500        20
           2     0.9167    0.9167    0.9167        12

    accuracy                         0.9556        45
   macro avg     0.9556    0.9556    0.9556        45
weighted avg     0.9556    0.9556    0.9556        45

= src/configs/Planter_config.json is generated
Exact match entries:  200
Ternary match entries:  54
+ Which architecture do you use? (default = tna | options = -h) v1model
+ Which is the use case? (default = performance | options = -h) performance
= Add the following path: /home/takuan/Planter/src/use_cases/performance
= Add the following path: /home/takuan/Planter/src/architectures/v1model
Generating p4 files and load data file... Done
+ What is the target device? (default = Tofino | options = -h) bmv2
+ Which type of mode do you want to choose? (default = software | options = -h) software
= Dump the targets info to src/configs/Planter_config.json
= Add the following path: /home/takuan/Planter/src/targets/bmv2/software
- Send packets to which port? (default = 'eth0'): eth0
- Please input your password for 'sudo' command: 
current pid is 102543
Generating test files ...
Join all subprocess together ...
sudo mn -c
*** Removing excess controllers/ofprotocols/ofdatapaths/pings/noxes
killall controller ofprotocol ofdatapath ping nox_corelt-nox_core ovs-openflowd ovs-controllerovs-testcontroller udpbwtest mnexec ivs ryu-manager 2> /dev/null
killall -9 controller ofprotocol ofdatapath ping nox_corelt-nox_core ovs-openflowd ovs-controllerovs-testcontroller udpbwtest mnexec ivs ryu-manager 2> /dev/null
pkill -9 -f "sudo mnexec"
*** Removing junk from /tmp
rm -f /tmp/vconn* /tmp/vlogs* /tmp/*.out /tmp/*.log
*** Removing old X11 tunnels
*** Removing excess kernel datapaths
ps ax | egrep -o 'dp[0-9]+' | sed 's/dp/nl:/'
***  Removing OVS datapaths
ovs-vsctl --timeout=1 list-br
ovs-vsctl --timeout=1 list-br
*** Removing all links of the pattern foo-ethX
ip link show | egrep -o '([-_.[:alnum:]]+-eth[[:digit:]]+)'
ip link show
*** Killing stale mininet node processes
pkill -9 -f mininet:
*** Shutting down stale tunnels
pkill -9 -f Tunnel=Ethernet
pkill -9 -f .ssh/mn
rm -f ~/.ssh/mn/*
*** Cleanup complete.
rm -f *.pcap
rm -rf build pcaps logs
mkdir -p build pcaps logs
p4c-bm2-ss --p4v 16 --p4runtime-files build/RF_performance_Iris.p4.p4info.txt -o build/RF_performance_Iris.json RF_performance_Iris.p4
RF_performance_Iris.p4(200): [--Wwarn=unused] warning: 'drop' is unused
    action drop() {
           ^^^^
[--Wwarn=deprecated] warning: .txt format is being deprecated; use .txtpb instead
sudo /home/takuan/src/p4dev-python-venv/bin/python3 ../../utils/run_exercise.py -t topology.json -j build/RF_performance_Iris.json -b simple_switch_grpc
Reading topology file.
Building mininet topology.
Configuring switch s1 with file s1-commands.txt
s1 -> gRPC port: 50051
**********
h1
default interface: eth0 10.0.1.1        08:00:00:00:01:01
**********
Starting mininet CLI

======================================================================
Welcome to the BMV2 Mininet CLI!
======================================================================
Your P4 program is installed into the BMV2 software switch
and your initial runtime configuration is loaded. You can interact
with the network using the mininet CLI below.

To inspect or change the switch configuration, connect to
its CLI from your host operating system using this command:
  simple_switch_CLI --thrift-port <switch thrift port>

To view a switch log, run this command from your host OS:
  tail -f /home/takuan/Planter/src/targets/bmv2/software/model_test/test_environment/logs/<switchname>.log

To view the switch output pcap, check the pcap files in /home/takuan/Planter/src/targets/bmv2/software/model_test/test_environment/pcaps:
 for example run:  sudo tcpdump -xxx -r s1-eth1.pcap

To view the P4Runtime requests sent to the switch, check the
corresponding txt file in /home/takuan/Planter/src/targets/bmv2/software/model_test/test_environment/logs:
 for example run:  cat /home/takuan/Planter/src/targets/bmv2/software/model_test/test_environment/logs/s1-p4runtime-requests.txt

mininet> Predicted load table time ... (0.039s)

==============================================================================================
=        Two steps to exit Planter: 1. input 'exit', press 'return'; 2. press ctrl + c       =
==============================================================================================
Test the switch model, the result should be the same as previous python test
Switch model 1th prediction: 0, test_y: 0, with acc: 1.0, with acc to sklearn: 1.0, with eSwitch model 2th prediction: 0, test_y: 0, with acc: 1.0, with acc to sklearn: 1.0, with eSwitch model 3th prediction: 0, test_y: 0, with acc: 1.0, with acc to sklearn: 1.0, with eSwitch model 4th prediction: 2, test_y: 2, with acc: 1.0, with acc to sklearn: 1.0, with eSwitch model 5th prediction: 1, test_y: 1, with acc: 1.0, with acc to sklearn: 1.0, with eSwitch model 6th prediction: 1, test_y: 2, with acc: 0.833, with acc to sklearn: 1.0, withSwitch model 7th prediction: 1, test_y: 1, with acc: 0.857, with acc to sklearn: 1.0, withSwitch model 8th prediction: 1, test_y: 1, with acc: 0.875, with acc to sklearn: 1.0, withSwitch model 9th prediction: 2, test_y: 2, with acc: 0.889, with acc to sklearn: 1.0, withSwitch model 10th prediction: 0, test_y: 0, with acc: 0.9, with acc to sklearn: 1.0, with Switch model 11th prediction: 2, test_y: 2, with acc: 0.909, with acc to sklearn: 1.0, witSwitch model 12th prediction: 0, test_y: 0, with acc: 0.917, with acc to sklearn: 1.0, witSwitch model 13th prediction: 0, test_y: 0, with acc: 0.923, with acc to sklearn: 1.0, witSwitch model 14th prediction: 2, test_y: 2, with acc: 0.929, with acc to sklearn: 1.0, witSwitch model 15th prediction: 2, test_y: 2, with acc: 0.933, with acc to sklearn: 1.0, witSwitch model 16th prediction: 1, test_y: 1, with acc: 0.938, with acc to sklearn: 1.0, witSwitch model 17th prediction: 1, test_y: 1, with acc: 0.941, with acc to sklearn: 1.0, witSwitch model 18th prediction: 1, test_y: 1, with acc: 0.944, with acc to sklearn: 1.0, witSwitch model 19th prediction: 0, test_y: 0, with acc: 0.947, with acc to sklearn: 1.0, witSwitch model 20th prediction: 2, test_y: 2, with acc: 0.95, with acc to sklearn: 1.0, withSwitch model 21th prediction: 1, test_y: 1, with acc: 0.952, with acc to sklearn: 1.0, witSwitch model 22th prediction: 0, test_y: 0, with acc: 0.955, with acc to sklearn: 1.0, witSwitch model 23th prediction: 1, test_y: 1, with acc: 0.957, with acc to sklearn: 1.0, witSwitch model 24th prediction: 1, test_y: 1, with acc: 0.958, with acc to sklearn: 1.0, witSwitch model 25th prediction: 1, test_y: 1, with acc: 0.96, with acc to sklearn: 1.0, withSwitch model 26th prediction: 1, test_y: 1, with acc: 0.962, with acc to sklearn: 1.0, witSwitch model 27th prediction: 1, test_y: 1, with acc: 0.963, with acc to sklearn: 1.0, witSwitch model 28th prediction: 2, test_y: 2, with acc: 0.964, with acc to sklearn: 1.0, witSwitch model 29th prediction: 0, test_y: 0, with acc: 0.966, with acc to sklearn: 1.0, witSwitch model 30th prediction: 0, test_y: 0, with acc: 0.967, with acc to sklearn: 1.0, witSwitch model 31th prediction: 2, test_y: 2, with acc: 0.968, with acc to sklearn: 1.0, witSwitch model 32th prediction: 1, test_y: 1, with acc: 0.969, with acc to sklearn: 1.0, witSwitch model 33th prediction: 2, test_y: 2, with acc: 0.97, with acc to sklearn: 1.0, withSwitch model 34th prediction: 1, test_y: 1, with acc: 0.971, with acc to sklearn: 1.0, witSwitch model 35th prediction: 2, test_y: 2, with acc: 0.971, with acc to sklearn: 1.0, witSwitch model 36th prediction: 1, test_y: 1, with acc: 0.972, with acc to sklearn: 1.0, witSwitch model 37th prediction: 1, test_y: 1, with acc: 0.973, with acc to sklearn: 1.0, witSwitch model 38th prediction: 1, test_y: 1, with acc: 0.974, with acc to sklearn: 1.0, witSwitch model 39th prediction: 1, test_y: 1, with acc: 0.974, with acc to sklearn: 1.0, witSwitch model 40th prediction: 2, test_y: 2, with acc: 0.975, with acc to sklearn: 1.0, witSwitch model 41th prediction: 0, test_y: 0, with acc: 0.976, with acc to sklearn: 1.0, witSwitch model 42th prediction: 0, test_y: 0, with acc: 0.976, with acc to sklearn: 1.0, witSwitch model 43th prediction: 0, test_y: 0, with acc: 0.977, with acc to sklearn: 1.0, witSwitch model 44th prediction: 2, test_y: 1, with acc: 0.955, with acc to sklearn: 1.0, witSwitch model 45th prediction: 1, test_y: 1, with acc: 0.956, with acc to sklearn: 1.0, with error: 0.0, M/A format macro f1: 0.9556, macro f1: 0.9556

               precision    recall  f1-score   support

           0     1.0000    1.0000    1.0000        13
           1     0.9500    0.9500    0.9500        20
           2     0.9167    0.9167    0.9167        12

    accuracy                         0.9556        45
   macro avg     0.9556    0.9556    0.9556        45
weighted avg     0.9556    0.9556    0.9556        45

======================================= Test Finished ========================================
mininet> 
Process <load data> cost 0.0022s
Process <train model> cost 0.0148s
Process <convert model> cost 0.0158s
Process <python-based test> cost 0.0547s
```

</details>

---

## Bug Fix Discovered During Qualification: PR #7

While running the qualification task on Python 3.12 / Ubuntu 24.04, I encountered a `KeyError: 0` crash in `table_generator.py`. I identified the root cause, fixed it across the entire codebase, and submitted a pull request.

### Root cause

`packages.txt` pins `pandas==1.1.3` (released 2020). In pandas v2.0+, `.max()[0]` raises `KeyError: 0` because `.max()` returns a `Series` indexed by column name, not position:

```python
# Pattern used in all 51 table_generator.py files — breaks on pandas >= 2.0:
t_t = [test_X[[i]].max()[0], train_X[[i]].max()[0]]
```

### Fix

```python
# Compatible with both pandas v1.x and v2.x:
t_t = [test_X[[i]].max().iloc[0], train_X[[i]].max().iloc[0]]
```

### Scope

Applied to all **51 `table_generator.py` files** across every ML module: `RF`, `DT`, `XGB`, `SVM`, `KNN`, `KM`, `NN`, `Bayes`, `IF`, `PCA`, `Autoencoder`

**PR #7:** https://github.com/In-Network-Machine-Learning/Planter/pull/7

---

## Dependency Issue: Issue #8

As a follow-up, I filed an issue proposing that all pinned dependencies in `packages.txt` be updated for Python 3.12 compatibility. All current pins are from 2019–2020:

```
scipy==1.5.2      # Released 2020
pandas==1.1.3     # Released 2020
numpy==1.19.2     # Released 2020
torch==1.4.0      # Released 2020
xgboost==0.90     # Released 2019
matplotlib==3.3.2 # Released 2020
```

**Issue #8:** https://github.com/In-Network-Machine-Learning/Planter/issues/8

---

## Repository Structure

```
.
├── README.md
└── screenshots/
    ├── matrix1_sklearn.png   ← Matrix 1: sklearn accuracy
    ├── matrix2_table.png     ← Matrix 2: P4 table logic simulation
    └── matrix3_bmv2.png      ← Matrix 3: BMv2 switch deployment
```
