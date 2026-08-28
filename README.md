Python code for the trace estimate method to compute the logarithm determinant of ring polymer hessian matrix.

We can then obtain the fluctuation factor from the log determinant.

Author: Chenghao Zhang.

**How to Use**
Change the folder path to a given folder and it will uses the hessian info in pickle file format to compute the trace estimate.

Options:
- trace estimate on original hessian matrix (large variance)
- trace estimate with control variate (small variance)
- trace estimate with control variate + subspace projection (tiny variance)
- estimate_logdert_std_bool: estimate the standard deviation of trace estimate by analyzing the statistics of  the trace estimate calculation run by 20 times.


**Key Features**
- Stochastic Trace Estimator to estimate the trace of positive definite matrix.
- Control Variate method to reduce the variance of the trace estimate
- Subspace projection method to reduce the variance of the trace estimate.
- Preconditioned Davidson algorithm to obtain the lowest eigenpair of ring polymer hessian matrix.

**Core Implementation**

***Trace Estimator***

The Trace Estimator class is derived from the base class:

Relationship between different classes:

BaseTraceEstimator -> TraceEstimator -> SubspaceProjTraceEstimator

BaseTraceEstimator -> ControlVariateLogDetEstimator -> SpringCVLogDetEstimator -> SpringCVSubSpaceLogDetEstimator

- BaseTraceEstimator: Abstract class for trace estimator.

- TraceEstiamtor: class that perform the trace estimate use stochastic trace estimation

- SubspaceProjTraceEstimator: Use subspace projection method to perform variance reduced trace estimate.The trace in the projected subspace is computed exactly. The trace in the complement subspace is computed using the stochastic trace estimate.

- ControlVariateLogDetEstimator: Abstract class derived from BaseTraceEstimator to use the control variate method to perform the variance reduced trace estimation.

- SpringCVLogDetEstimator: Implementation of ControlVariateLogDetEstimator which uses the spring hessian term as the control variate. 

- SpringCVSubspaceLogDetEstimator: Derived class from SpringCVLogDetEstimator, the log determinant is computed with subspace projection on top of control variate method. It uses SubspaceProjTraceEstimator class in its implementation to compute the trace. 


***LinearOperator***

The Ring Polymer Hessian matrix is represented as LinearOperator (https://linear-operator.readthedocs.io/en/latest/index.html) instead of the dense matrix. 

To implement the Ring Polymer Hessian matrix, we also define several LinearOperator class derived from the Base Linear Operator class:

- BaseCoupledOscillator: 

Abstract class, it performs the following operation: fft -> f(x) -> ifft.

- SqrtInvCoupledOscillator: Implementation of the BaseCoupledOscillator, here f(x) = 1/sqrt(x)

This is used to perform B^{-1/2}: the coupled spring Hessian matrix along each physical dimension.

- InvShiftedCoupledOscillator: Implementation of the BaseCoupledOscillator, here f(x) = 1/(x-theta):

This is used to form the preconditioner of Davidson algorithm. 

- BaseCoupledOscillatorLinearOperator:

Abstract class, it transform the (NP) dimension vector into (N,P) vector and uses BaseCoupledOscillator to do fft along each row of the data. Used on multidimensional coupled harmonic oscillator.

- SqrtInvCoupledOscillatorLinearOperator:

Implementation of the BaseCoupledOscillatorLinearOperator:
Uses SqrtInvCoupledOscillator along each physical dimension. This is for control variate method.

- InvShiftedCoupledOscillatorLinearOperator:

Implementation of the BaseCoupledOscillatorLinearOperator:
Uses InvShiftedCoupledOscillator along each physical dimension. This is for the preconditioned Davidson algorithm.

- SparseLinearOperator:

Linear Operator that stores the sparse matrix. 

**Preconditioned Davidson Algorithm**
The Preconditioner is given by DavidsonPreconditioner() class.
davodson algorithm is implemented with preconditioner. Adapted from https://joshuagoings.com/2013/08/23/davidsons-method/. Acknowledge to Joshua Goings.


