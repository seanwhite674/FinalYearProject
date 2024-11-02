This is Seán White's Final Year Project Git-Hub Repository.

Below is a description of the project

Applications of Gaussian Process Regression for

Gravitational Waves
Sarp Akcay

In the last few decades, Gaussian processes have become an indispensable
tool to computational science [1]. An important application is their use in model
fitting known as Gaussian Process Regression (GPR). The method is extremely
powerful, especially when the data sets used for fitting (model building) are
irregular or deficient. Naturally, GPR has found its way into gravitational wave
astronomy (see e.g., [2, 3, 4, 5]).
For this particular project, the goal is to build a GPR model predicting the

unfaithfulness (mismatch) of gravitational waveform models to numerical rela-
tivity simulations. The dependent variable, i.e., the data, is the mismatch which

is constructed from a eight-dimensional intrinsic binary black hole parameter
space. The eight parameters are the black hole masses and the two black hole

(Newtonian) spin vectors with three components each. Proof-of-principle para-
metric fits to such mismatches have been constructed (see App. A of Ref. [6])

with better fitting functions to be employed in the upcoming Ref. [7].
Given the irregularity of the intrinsic parameter space and the fact that
none of the parameters are correlated and that they can each be drawn from
a normal distribution, GPR is the ideal method for improving these fits. GPR
also has the added benefit that each new data point can be straightforwardly be
added to the model no matter how far away it may be in the parameter space
compared to the previous data points.
The student would start by familiarizing themselves with GPR via, e.g.,
Refs. [1, 8, 9]. They would then work on a toy problem of building a GPR for
a data set dependent on not one, but two variables (see e.g., Ref. [11]). Once
they have a good control and understanding of this, they can then tackle the
full problem involving the eight independent variables determining the values
of the mismatch.
This project is better suited to students with either some background or
interest in statistics as well as coding. Most of the work would be carried out
in python with some Mathematica as well. The project also has potential to be
absorbed into an upcoming research paper due to be submitted for publication
in the Spring.
References
[1] C. E. Rasmussen and C. K. I. Williams, “Gaussian Processes for Machine
Learning”. The MIT Press, (2006).
[2] C. J. Moore, C. P. L. Berry, A. J. K. Chua and J. R. Gair, Phys. Rev. D
93 (2016) no.6, 064001, [arXiv:1509.04066 [gr-qc]].

1

Figure 1: Illustrative example of the output from a GPR used to build a model
dependent on a single parameter ✓. Figure taken from Ref. [12].
[3] A. J. K. Chua, N. Korsakova, C. J. Moore, J. R. Gair and S. Babak,
“Gaussian processes for the interpolation and marginalization of waveform
error in extreme-mass-ratio-inspiral parameter estimation,” Phys. Rev. D
101 (2020) no.4, 044027, [arXiv:1912.11543 [astro-ph.IM]].
[4] https://daniel-williams.co.uk/project/heron/.
[5] D. Williams, I. S. Heng, J. Gair, J. A. Clark and B. Khamesra, Phys. Rev.
D 101 (2020) no.6, 063011, [arXiv:1903.09204 [gr-qc]].
[6] J. Mac Uilliam, S. Akcay and J. E. Thompson, Phys. Rev. D 109 (2024)
no.8, 084077, [arXiv:2402.06781 [gr-qc]].
[7] C. Hoy, S. Akcay, J. Mac Uilliam, and J. E. Thompson, “Incorporating

numerical relativity into gravitational-wave Bayesian inference”, in prepa-
ration.

[8] J. Wang, “An Intuitive Tutorial to Gaussian Process Regression”, https:
//arxiv.org/2009.10862v5.
[9] J. Wang, “Gaussian-Process-Regression-Tutorial Public”, https://
github.com/jwangjie/Gaussian-Process-Regression-Tutorial.
[10] https://thegradient.pub/gaussian-process-not-quite-for-dummies/
[11] https://jamesbrind.uk/posts/2d-gaussian-process-regression/.

[12] Leclercq, Florent. (2018). Bayesian optimization for likelihood-free cos-
mological inference. Physical Review D. 98, 063511, [arXiv:1805.07152

[astro-ph.CO]]
