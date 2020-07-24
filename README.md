## COVID19TestProbability Function
The program uses of Sequential Bayesian Updating to calculate the probability a user is COVID19 positive or negative for PCR based upon a set of conditions the user inputs into the function.


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

### Limitations

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

* Download the RMD File

* Run each chunk in order 

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

* Thanks Kelsey Maccuish for helping with testing.

* References to Kurcika et al. https://www.acpjournals.org/doi/10.7326/M20-1495






