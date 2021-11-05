## COVID19TestProbability Function
The project is a first attempt at a Sequential Bayesian Updating program to calculate the probability a user is COVID19 positive or negative for PCR based upon a set of conditions the user inputs into the function.


<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Motivations and Purpose](#motivations-and-purpose)
  * [Limitations](#limitations)
* [Getting Started](#getting-started)
  * [Requirements](#requirements)
  * [Code Organization](#code-organization)
  * [Set Up](#set-up)
  * [Usage](#usage)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- ABOUT THE PROJECT -->
## About the Project

### Motivations and Purpose

Due to the ongoing COVID19 pandemic, there is widespread interest in understanding the true probabilities of being positive or negative given a set of PCR test results. Furthermore, research indicates there is a disturbingly high false negative rate with current PCR tests (see references). 

This project aims to create a program that will allow a user to understand what the true probability of being COVID19 positive or negative is given a series of PCR test results. 

Testing diagnostics are typically given in sensitivities Pr(Patient Tests Positive | Patient is Positive)  and specificities  Pr(Patient Tests Negative | Patient is Negative). However, while in the right context this information is of great value, it is of little value to the patient being tested and is often misleading to medical staff. The information the patient is actually concerned with is the Pr(Patient is Positive | Positive Test) or Pr(Patient is Negative | Negative Test). These probabilities are also known as the positive predictive value (PPV) and the negative predictive value (NPV) of a test.

Using Bayes Rule: 
Pr⁡(A | B)=(Pr⁡(B│A)*Pr⁡(A))/(Pr⁡(B))

Posterior=(Likelihood*Prior)/Evidence

We can convert the sensitivities and specificities into the PPV and NPV. During the first iteration the sensitivity is the likelihood, the prior is the disease prevalence, and the posterior is the PPV. However, in subsequent iterations, the old posterior becomes the new prior. We can therefore sequentially update the probability of being positive or negative given a sequence of tests by iteratively using Bayes Rule. See file “Bayesian Sequential Updating for Disease Tests” for further discussion.


### Limitations

Typically, Sequential Bayesian Updating uses priors from probability distributions based upon statistical data. Due to the rapidly evolving COVID19 situation, as well as the difficulties involved in estimating disease prevalence of an ongoing novel pandemic, this current version of the program assumes the prevalence of COVID19 is a fixed number and not a probability distribution for simplicity. It also assumes that all values in the calculations are fixed numbers, with the confidence interval being constructed from the estimates in the Kurcika et al. paper. 

Additionally, according to reports by A.P., the CDC estimates that prevalence may be as high as 10x the current positive test rates. Prevalence data comes from the World-o-Meters website, multiplying the number of active cases in each region by 10.  It should be noted that the “active cases” section on the World-o-Meters site is not rigorously updated and is updated slowly, making it at best a decent guess. This method was chosen for this version for simplicity and accessibility

Furthermore, the program assumes homogenous stochastic mixing between the populations of each region. This assumption is a commonly used assumption in most disease models including the compartmentalized disease models such as the SIR models. Therefore, this assumption is a minor one, but should not be ignored.

The final major assumptions surround the PCR tests themselves. The first of these assumes that the tests were all taken at the same point in time. Additionally, the model assumes that all tests are independent of each other. Naturally this assumption is not upheld, as the main cause for the high false negative rates for the PCR test is due to test administrators not obtaining a robust enough sample. Therefore, a negative test due to constricted nasal passages of a patient will not be independent from the next negative test.

The major assumption violations of the current program prevent the numbers from being truly accurate. However, many of the egregious assumptions can be improved or eliminated as time goes on. As prevalence data becomes more complete and more accurate, the initial priors can be corrected to be closer to reality. Further, as more data is collected, the priors can be updated to true probability distributions. The time dependence assumption of the program can also be updated with minimal effort to account for when tests were taken, weighting the priors based upon the prevalences of when the tests were conducted. Finally, the independence of tests assumption can easily be modified to include patient symptoms and personal information as such data becomes available.

In a clinical or other sensitive setting, the program in its current form will not be suitable for application. At the moment it will give the user a better “educated guess” at what their true probability of being COVID19 positive or negative really is, so long as the user is not concerned with the exact probability. Further, the program is fairly easy to modify as more data becomes available and more accurate, thus it can serve as a skeleton framework for future projects. 


<!-- GETTING STARTED -->
## Getting Started

### Requirements

* R

Function created and tested with R 4.0.2

### Code organization 

The RMD file contains 5 chunks, each containing their own function. The final chunk contains the main COVIDTestProbability function code. All previous chunks contain the functions the main function calls upon to complete and return its calculations.

  * The first chunk contains the “DFFiller” function, which will input into a data frame the point, upper bound, and lower bound estimates for COVID19 PCR test sensitivity based upon a study by Kurcika et al.

  * The second chunk contains the “DFconstructor” function, which will build the main data frame used to store relevant values for calculations. This function calls upon the DFFiller function within it.

  * The third chunk contains the “Updater” function, which uses Sequential Bayesian Updating to calculate the probability the user is COVID19 positive or negative based upon their test sequence.

  * The fourth chunk contains the “fetcher” function, which checks to see if the user asked for probability of being positive or negative and checks to see number of days the user has had symptoms. From this information it will select and return the indices of interest to the user along with some text for easy interpretation.

  * The fifth chunk contains the “COVIDTestProb” function, which is the main function. This function uses all previous functions to return requested values to the user. Note the returned results are vectorized, thus allowing a user to obtain the numeric results on their own if necessary.

See comments in RMD file for more details.

### Set Up

* Download the RMD File.

* Run each must initially be run IN ORDER in the RMD file.

* Once the final chunk is run, the main function COVIDTestProbability will be ready for use.

### Usage

In order to use the COVIDTestProbability function, the user must input a set of conditions into the following arguments.

1. Enter whether the function should calculate the probability of being positive or negative into the `Want` argument.

2. Enter how many days the patient of interest has had symptoms into the `Time` argument. If the patient has no symptoms enter `Time = 0` and if the patient has recovered from symptoms enter `Time = -1`

3. Enter a vector containing the sequence of test results in order of when the tests were administered into the `TestVec` argument. Negative tests should be entered as a single capital "N" and positives as a single capital "P".  A test vector of two negative tests and then two positives should be entered as follows:

```JS
TESTVEC <- c("N", "N", "P", "P")
COVIDTESTProbability(TestVec = TESTVEC,...)
```

4. Enter the the capitalized state abbreviation for the state in which they were potentially exposed to COVID19 into the `Region` argument. Note version 1.0.0 only supports GA, NY, CA, AZ, NC, TX, VA, FL.

#### Example 

Input:
```JS
TESTVEC <- c("N", "N", "P")
COVIDTESTProbability(Want = "Positive", Time = 1, Region = "FL", TestVec = TESTVEC)
```
Return:

```JS
[1] The percent chance you are positive is estimated to be
[2] 93.7                                                  
[3] and with 95% confidence is between                    
[4] 51.6                                                  
[5] and                                                   
[6] 98.7  
```
<!-- CONTACT -->
## Contact

Author: Sean Steele

Email: sean.steele.a@gmail.com

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements

* Thanks to Kelsey Maccuish for helping with testing.

* References to Kurcika et al. https://www.acpjournals.org/doi/10.7326/M20-1495

* References to Andrew N. Cohen, Bruce Kessel https://www.medrxiv.org/content/10.1101/2020.04.26.20080911v1.full.pdf

* References to AP reporting on CDC estimates for COVID19 serological prevalence https://www.nwherald.com/2020/07/21/cdc-antibody-tests-show-virus-rates-10x-higher/afvoinh/





