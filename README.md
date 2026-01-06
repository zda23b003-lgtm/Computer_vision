# Lossless Compression of Human Skeleton Joint Sequences
# Project P15 – Computer Vision

Author: Umniya Maulid
Roll No:Zda23b003
Course / University: Computer vision / IITMadras Zanzibar

## 1. Project Overview

This project implements an end-to-end pipeline for strict lossless compression of human skeleton joint sequences.
The method exploits:

temporal smoothness of human motion,

kinematic hierarchy of skeleton joints (parent–child structure),

and entropy coding of prediction residuals.

The goal is to reduce storage size while guaranteeing exact reconstruction under a fixed-point numeric representation.

All experiments are conducted on the NTU RGB+D Skeleton Dataset.

## 2. Key Contributions

A fixed-point formulation that enables strict lossless compression.

A hierarchical parent–child motion predictor that exploits skeleton structure.

A comprehensive ablation study:

temporal vs hierarchical prediction,

naive storage vs Huffman vs entropy bound.

Quantitative evaluation with verified exact reconstruction.

## 3. Dataset
NTU RGB+D Skeleton Dataset

Each sequence contains 25 joints, each with 3D coordinates (x, y, z).

Data shape per sample:

(T, 25, 3)


where T is the number of frames.

Preprocessing

Pelvis-centered normalization (joint 0 used as reference).

Validation and cleaning:

NaN / Inf detection

degenerate or static sequences

missing or zero-valued joints

Clean sequences are stored as .npy files for efficient loading.

## 4. Methodology
4.1 Fixed-Point Representation (Strict Lossless)

To avoid floating-point precision issues, skeleton coordinates are converted to integers:

𝑋
(
int
)
=
r
o
u
n
d
(
𝑠
⋅
𝑋
)
,
𝑠
=
10
4
X
(int)
=round(s⋅X),s=10
4

All prediction, encoding, and reconstruction operate in the integer domain.

Strict lossless reconstruction is verified by:

𝑋
^
(
int
)
=
𝑋
(
int
)
X
^
(int)
=X
(int)
4.2 Baseline Predictor: Temporal Delta

Each frame is predicted from the previous frame:

𝑋
^
𝑡
=
𝑋
𝑡
−
1
,
𝑅
𝑡
=
𝑋
𝑡
−
𝑋
𝑡
−
1
X
^
t
	​

=X
t−1
	​

,R
t
	​

=X
t
	​

−X
t−1
	​


Store the first frame and residuals.

Exact reconstruction is achieved in the integer domain.

4.3 Proposed Predictor: Hierarchical Parent–Child Motion

Let 
𝑝
(
𝑗
)
p(j) denote the parent of joint 
𝑗
j in the skeleton tree:

𝑋
^
𝑡
,
𝑗
=
𝑋
𝑡
−
1
,
𝑗
+
(
𝑋
𝑡
,
𝑝
(
𝑗
)
−
𝑋
𝑡
−
1
,
𝑝
(
𝑗
)
)
X
^
t,j
	​

=X
t−1,j
	​

+(X
t,p(j)
	​

−X
t−1,p(j)
	​

)

Residual:

𝑅
𝑡
,
𝑗
=
𝑋
𝑡
,
𝑗
−
𝑋
^
𝑡
,
𝑗
R
t,j
	​

=X
t,j
	​

−
X
^
t,j
	​


This captures kinematic coupling, producing lower-entropy residuals than temporal delta coding.

4.4 Entropy Coding

Residual symbols are encoded using:

Huffman coding (practical entropy estimate),

Entropy bound (ideal arithmetic coding lower bound).

Note: The “arithmetic” results represent a theoretical lower bound, not a full arithmetic coder implementation.

## 5. Evaluation Metrics
Compression Ratio (CR)
C
R
=
Original bits
Compressed bits
CR=
Compressed bits
Original bits
	​

Compression Percentage (Storage Reduction)
Compression %
=
(
1
−
1
C
R
)
×
100
Compression %=(1−
CR
1
	​

)×100

Original size is computed using float32 skeleton storage (32 bits per value).

All reported strict-lossless results achieve 100% exact reconstruction in the fixed-point domain.

## 6. Results (Strict Lossless)
Average Results on NTU RGB+D
Method	CR (Naive)	CR (Huffman)	CR (Entropy Bound)	Compression %	Lossless
Temporal Delta	1.9969	3.6152	4.0809	~72%	Yes
Hierarchical (Proposed)	1.9969	3.8918	4.4360	~74–78%	Yes

Key observations:

Hierarchical prediction consistently outperforms temporal delta.

Entropy coding significantly improves compression.

Exact reconstruction is verified for all sequences.

## 7. Ablation Study

The notebook includes ablations on:

Prediction model: Temporal vs Hierarchical

Coding strategy: Naive vs Huffman vs Entropy bound

Representation: Float32 vs Fixed-point integers

These ablations directly demonstrate the contribution of each component.

## 8. Comparison with Prior Work

Representative skeleton compression methods report 20–40% storage reduction, typically measured relative to already quantized or fixed-point representations.

In contrast, this work reports compression relative to float32 storage, which explains the higher percentages while still maintaining strict lossless reconstruction.

This comparison positions the proposed method relative to existing approaches while maintaining fair interpretation.

## 9. How to Run the Code

Open the notebook:

CVProject.ipynb


Set the dataset path at the top of the notebook.

Run all cells top to bottom.

Generated outputs:

strict_lossless_per_file.csv

strict_lossless_summary.csv

These CSV files are used to generate tables for the report and slides.

## 10. Notes and Limitations

Entropy-bound results are theoretical lower limits.

Fixed-point scale is chosen manually (precision vs range trade-off).

No learned or deep compression models are used; the focus is on signal- and structure-based compression.

## 11. References

NTU RGB+D Skeleton Dataset

J. Lin et al., Adaptive Lossless Compression of Human Skeleton Sequences, IEEE TCSVT, 2020

J. Hou et al., 3D Human Skeleton Data Compression for Action Recognition, ICME, 2019

## 12. Summary

This project demonstrates a reproducible, strict lossless skeleton compression pipeline.
By exploiting skeleton hierarchy and entropy coding, it achieves significant storage reduction while guaranteeing exact reconstruction.
