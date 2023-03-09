# The Means to Attain Dependability
#dependability  

Four techniques can be utilized to develop a dependable computing system.

## Fault Prevention
Fault prevention deals with how to prevent the occurrence or introduction of faults. Quality control techniques can be employed to attain fault prevention. The exact techniques depend on the aspect of the system in question, such as software, hardware, physical, or malicious faults.

## [[Fault Tolerance]]

## [[Fault Removal]]

## Fault Forecasting
Fault forecasting deals with evaluating the present number, future incidence, and likely consequences of faults. Evaluation can be qualitative, as conducted through failure mode and effect analysis (FMEA)) or quantitative, as through [[Markov Chain|Markov chains]] and [[Stochastic Petri Net|stochastic Petri nets]]). Some techniques like reliability block diagrams and fault trees can be used for both kinds of evaluations.

*Failure intensity* is a measure of the frequency of failures (per unit time) as noticed by the users. Failure intensity is usually cyclical. It first decreases, then stabilizes, and finally increases before the cycle repeats itself. In this context, the measures of dependability become:
- Reliability: A measure of continuous delivery of correct service, or the time of failure.
- Availability: A measure of the deliver of correct service with respect to the alternation of correct and incorrect service.
- Maintainability: A measure of time to service restoration since the last failure occurrence, or measure of continuous delivery of incorrect service.
- Safety: A measure of continuous safeness, i.e., lack of catastrophic failures, or the time to catastrophic failure.