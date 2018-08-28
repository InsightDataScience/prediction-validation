# Table of Contents
1. [Introduction](README.md#introduction)
1. [Challenge summary](README.md#challenge-summary)
1. [Details of challenge](README.md#details-of-challenge)
1. [Input files](README.md#input-files)
1. [Output file](README.md#output-file)
1. [Example](README.md#example)
1. [Writing clean, scalable and well-tested code](README.md#writing-clean-scalable-and-well-tested-code)
1. [Repo directory structure](README.md#repo-directory-structure)
1. [Testing your directory structure and output format](README.md#testing-your-directory-structure-and-output-format)
1. [Instructions to submit your solution](README.md#instructions-to-submit-your-solution)
1. [FAQs](README.md#faqs)



## Introduction

You are a data engineer working at a financial institution that analyzes real-time stock market data. To determine the best trading strategy, the company's data scientists created a machine learning model to predict the future price of a stock every hour, and they want to test it on real-time stock data.

Before deploying the model, they want you to help test how accurate their predictions are over time by comparing their predictions with newly arriving real-time stock prices.


## Challenge summary

You will read two different files, one provides the actual value of each stock every hour and the second lists the predicted value of various stocks at a certain hour during the same time period.

You will obtain the `average error` by calculating the average difference between the actual stock prices and predicted values over a specified sliding time window.


## Details of challenge

You are given the following input files

1. `actual.txt`: A time-ordered file listing the actual value of stocks at a given hour.
1. `predicted.txt`: A time-ordered file of the predicted value of certain stocks at a given hour. 
1. `window.txt`: Holds a single integer value denoting the window size (in hours) for which you must calculate the `average error`.

You may have multiple stocks for the same hour. While stock prices in the real world can change every second, for the purposes of this challenge, you can assume they only change every hour.

You are expected to produce the following output file

1. `comparison.txt`: A time-ordered file containing the average error of stock predictions for a certain time period


### How are the predictions made?

The data scientists have trained a complex model to predict the price of every stock at a given time. They have set thresholds on how confident their predictions are and filtered out predictions that have a low confidence value. The file,`predicted.txt`, contains only **high confidence predictions**.


### Calculations

To compute the average error in a given time window, find the absolute difference between the actual and predicted values of every matched stock and time pair. Let's call this result `error`. You then compute the average of all errors within a certain time window to get the `average error`.

To better gauge the algorithm's performance, you are asked to compute this `average error` over a sliding window, moving one hour at a time until the end of the file.


## Input Files

Lines in both input files, `actual.txt` and `predicted.txt`, are listed in chronological order. Both contain the following pipe-delimited fields: 

* `time`: An integer greater than 0 designating the hour.
* `stock`: ID of a stock 
* `price`: Price of a given stock at the given hour.

The file, `window.txt`, will contain one value (an integer greater than 0), specifying the size of the sliding window in hours.


### Input file considerations

For this challenge, you may assume the following:

1. No headers for any file.
2. Sliding window values are inclusive (e.g., a sliding window of 2 would include data at hour 5 and hour 6)
1. Stocks prices are ordered by time (e.g., the price for a particular stock id `BJKRRX` at hour `100` will **NOT** appear in the file before hour `50`.

You should **NOT** assume that the stock ids will ever be sorted.

## Output File

You must create an output file named `comparison.txt` where each line has the following pipe-delimited fields:

1. Starting hour time window
1. Ending hour time window
1. `average error` rounded off to 2 decimal places. Given rounding issues some applicants have run into, we're going to accept values that are +/- 0.01 of the expected value. For example, if the expected value is 0.17, we will accept 0.16, 0.17, or 0.18 as the correct answer.

The output file also has no headers and the lines should be listed in chronological order.

## Example

To make reading the below example easier, there is a new line between each hour of information in `actual.txt` and `predicted.txt`. There will **NOT** be that extra new line in the actual input files we'll test your code on.

##### window.txt
```
2
```

##### actual.txt
```
1|SLKWVA|94.51
1|CMWTQH|81.27
1|ATAYJP|25.74
1|HVIWZR|22.81

2|ATAYJP|29.62
2|SLKWVA|81.87
2|CMWTQH|116.11
2|HVIWZR|22.15

3|ATAYJP|21.93
3|HVIWZR|22.24
3|SLKWVA|78.01
3|CMWTQH|113.63

```


##### predicted.txt
```
1|ATAYJP|25.71
1|HVIWZR|22.80
1|SLKWVA|94.49
1|CMWTQH|81.22

2|ATAYJP|29.92
2|HVIWZR|22.06

3|ATAYJP|21.84
3|HVIWZR|22.36
3|SLKWVA|79.49

```


This example provides the value for stocks `SLKWVA`, `CMWTQH`, `ATAYJP` and `HVIWZR` for hours `1, 2 and 3`.

You have noticed that the predicted file is shorter than the actual file. This is because it only contains high confidence predictions. 

In this example, because `window.txt` contains the value `2`, we will compute the `average error` over two hour time windows. 

We compare every stock and time pair in `actual.txt` with its companion in `predicted.txt` and calculate the `error` between them. If we do not have a match for a stock at a particular time, we ignore the row and continue. 

Below is an example of that comparison:

#### Time window: `1 to 2`
```
actual              predicted           error
1|ATAYJP|25.74      1|ATAYJP|25.71      0.03
1|SLKWVA|94.51      1|SLKWVA|94.49      0.02
1|CMWTQH|81.27      1|CMWTQH|81.22      0.05
1|HVIWZR|22.81      1|HVIWZR|22.80      0.01
2|SLKWVA|81.87      no data present     ignore
2|ATAYJP|29.62      2|ATAYJP|29.92      0.30
2|HVIWZR|22.15      2|HVIWZR|22.06      0.09
2|CMWTQH|116.11     no data present     ignore
```

#### Time window: `2 to 3`
```
actual              predicted           error
2|SLKWVA|81.87      no data present     ignore
2|ATAYJP|29.62      2|ATAYJP|29.92      0.30
2|HVIWZR|22.15      2|HVIWZR|22.06      0.09
2|CMWTQH|116.11     no data present     ignore
3|ATAYJP|21.93      3|ATAYJP|21.84      0.09
3|HVIWZR|22.24      3|HVIWZR|22.36      0.12
3|SLKWVA|78.01      3|SLKWVA|79.49      1.48
3|CMWTQH|113.63     no data present     ignore
```

### Calculating average error

We take the average of the error for each time window ignoring those entries that have no data present in `predicted.txt`. 

For the first time window, the `average error` is `0.08` or `(0.03 + 0.02 + 0.05 + 0.01 + 0.30 + 0.09)/6` 

And for the second time window, the `average error` is `0.42` or `(0.30 + 0.09 + 0.09 + 0.12 + 1.48)/5`

The results would be written to the output file as seen below. 

##### comparison.txt
```
1|2|0.08
2|3|0.42
```

### Testing your code
We will test your code on various scenarios and various sizes of input. Please ensure you have handled all edge cases that may arise. Write optimized code and make no assumptions regarding the size of the input or number of listings. 

Your code will be tested on a single machine. Using distributed technologies may negatively impact your solution. We're not testing your knowledge on distributed computing, but rather on computer science fundamentals and software engineering best practices. You may safely assume that the input data can fit into the main memory of one machine.


## Writing clean, scalable and well-tested code

As a data engineer, it’s important that you write clean, well-documented code that scales for large amounts of data. For this reason, it’s important to ensure that your solution works well for a large number of records, rather than just the above example.

It's also important to use software engineering best practices like unit tests, especially because data is not always clean and predictable. For more details about the implementation, please refer to the FAQ below. If further clarification is necessary, email us at <cc@insightdataengineering.com> but please do so only after you have read through the Readme and FAQ one more time and cannot find the answer to your question.

Before submitting your solution you should summarize your approach, dependencies and run instructions (if any) in your `README`.

You may write your solution in any mainstream programming language such as C, C++, C#, Clojure, Erlang, Go, Haskell, Java, Python, Ruby, or Scala. Once completed, submit a link to a Github repo with your source code.

In addition to the source code, the top-most directory of your repo must include the `input` and `output` directories, and a shell script named `run.sh` that compiles and runs the program(s) that implement the required features.

If your solution requires additional libraries, environments, or dependencies, you must specify these in your `README` documentation. See the figure below for the required structure of the top-most directory in your repo, or simply clone this repo.



## Repo directory structure

The directory structure for your repo should look like this:
```
.
├── README.md
├── run.sh
├── src
│   └── prediction-validation.py
├── input
│   ├── actual.txt
│   ├── predicted.txt
│   └── window.txt
├── output
│   └── comparison.txt
└── insight_testsuite
    ├── run_tests.sh
    └── tests
        ├── test_1
        │   ├── input
        │   │   ├── actual.txt
        │   │   ├── predicted.txt
        │   │   └── window.txt
        │   └── output
        │       └── comparison.txt
        │
        └── your_own_test_1
            ├── input
            │   ├── actual.txt
            │   ├── predicted.txt
            │   └── window.txt
            └── output
                └── comparison.txt
```

**Don't fork this repo** and don't use this `README` instead of your own. The content of `src` does not need to be a single file called `prediction-validation.py`, which is only an example. Instead, you should include your own source files and give them expressive names.

## Testing your directory structure and output format

To make sure that your code has the correct directory structure and the format of the output files are correct, we have included a test script called `run_tests.sh` in the `insight_testsuite` folder.

The tests are stored simply as text files under the `insight_testsuite/tests` folder. Each test should have a separate folder with an `input` folder for `actual.txt`, `predicted.txt`, `window.txt` and an `output` folder for `comparison.txt`.

You can run the test with the following command from within the `insight_testsuite` folder:

```
insight_testsuite~$ ./run_tests.sh 
```

On a failed test, the output of `run_tests.sh` should look like:
```
[FAIL]: test_1
[Thu Mar 30 16:28:01 PDT 2017] 0 of 1 tests passed
```

On success:
```
[PASS]: test_1
[Thu Mar 30 16:25:57 PDT 2017] 1 of 1 tests passed
```

Your submission must pass at least the provided test in order to pass the coding challenge.

For a limited time we also are making available a <a href="http://ec2-18-210-131-67.compute-1.amazonaws.com/test-my-repo-link">website</a> that will allow you to simulate the environment in which we will test your code. It has been primarily tested on Python code but could be used for Java and C++ repos. Keep in mind that if you need to compile your code (e.g., javac, make), that compilation needs to happen in the `run.sh` file of your code repository. For Python programmers, you are able to use Python2 or Python3 but if you use the later, specify `python3` in your `run.sh` script.

# Instructions to submit your solution
* To submit your entry please use the link you received in your coding challenge invite email
* You will only be able to submit through the link one time 
* Do NOT attach a file - we will not admit solutions which are attached files 
* Use the submission box to enter the link to your GitHub or Bitbucket repo ONLY
* Link to the specific repo for this project, not your general profile
* Put any comments in the README inside your project repo, not in the submission box
* We are unable to accept coding challenges that are emailed to us 

# FAQs

Here are some common questions we've received. If you have additional questions, please email us at `cc@insightdataengineering.com` and we'll answer your questions as quickly as we can (during PST business hours), and update this FAQ. Again, only contact us after you have read through the Readme and FAQ one more time and cannot find the answer to your question.

### What should I do when I encounter rounding off errors on comparingmy results with the testcase provided?
Given rounding issues some applicants have run into, we're going to accept values that are +/- 0.01 of the expected value. For example, if the expected value is 0.17, we will accept 0.16, 0.17, or 0.18 as the correct answer. 

### What if I am not able to find a match between actual and predicted values in a time window?
When you are not able to find a match between stock and price for a given window, please output to be "NA" for `average error`. For instance, if we do not have a match between actual and predicted stock values for the window between hours `3 and 5`, you should output the following.

```
3|5|NA
```

### Which Github link should I submit?
You should submit the URL for the top-level root of your repository. For example, this repo would be submitted by copying the URL `https://github.com/InsightDataScience/prediction-validation` into the appropriate field on the application. **Do NOT try to submit your coding challenge using a pull request**, which would make your source code publicly available.

### Do I need a private Github repo?
No, you may use a public repo, there is no need to purchase a private repo. You may also submit a link to a Bitbucket repo if you prefer.

### May I use R, Matlab, or other analytics programming languages to solve the challenge?
It's important that your implementation scales to handle large amounts of data. While many of our Fellows have experience with R and Matlab, applicants have found that these languages are unable to process data in a scalable fashion, so you must consider another language.

### May I use distributed technologies like Hadoop or Spark?
Your code will be tested on a single machine, so using these technologies will negatively impact your solution. We're not testing your knowledge on distributed computing, but rather on computer science fundamentals and software engineering best practices. 

### What sort of system should I use to run my program on (Windows, Linux, Mac)?
You may write your solution on any system, but your source code should be portable and work on all systems. Additionally, your `run.sh` must be able to run on either Unix or Linux, as that's the system that will be used for testing. Linux machines are the industry standard for most data engineering teams, so it is helpful to be familiar with this. If you're currently using Windows, we recommend installing a virtual Unix environment, such as VirtualBox or VMWare, and using that to develop your code. Otherwise, you also could use tools, such as Cygwin or Docker, or a free online IDE such as Cloud9.

### How fast should my program run?
While there are no strict performance guidelines to this coding challenge, we will consider the amount of time your program takes when grading the challenge. Therefore, you should design and develop your program in the optimal way (i.e. think about time and space complexity instead of trying to hit a specific run time value). 

### Can I use pre-built packages, modules, or libraries?
This coding challenge can be completed without any "exotic" packages.

### Will you email me if my code doesn't run?
Unfortunately, we receive hundreds of submissions in a very short time and are unable to email individuals if their code doesn't compile or run. We will do everything we can to properly test your code, but this requires good documentation. More so, we have provided a test suite so you can confirm that your directory structure and format are correct.

### Can I use a database engine?
This coding challenge can be completed without the use of a database.

### Do I need to use multi-threading?
No, your solution doesn't necessarily need to include multi-threading - there are many solutions that don't require multiple threads/cores or any distributed systems, but instead use efficient data structures.

### What should the format of the output be?
In order to be tested correctly, you must use the format described above. You can ensure that you have the correct format by using the testing suite we've included.


### Should I check if the files in the input directory are text files or non-text files(binary)?
No, for simplicity you may assume that all of the files in the input directory are text files, with the format as described above.

### Can I use an IDE like Eclipse or IntelliJ to write my program?
Yes, you can use whatever tools you want - as long as your `run.sh` script correctly runs the relevant target files and creates the `comparison.txt` file in the `output` directory.

### What should be in the input directory?
You can put any text file you want in the directory since our testing suite will replace it. Indeed, using your own input files would be quite useful for testing. The file size limit on Github is 100 MB so you won't be able to include the larger sample input files in your `input` directory.

### How will the coding challenge be evaluated?
Generally, we will evaluate your coding challenge with a testing suite that provides a variety of inputs and checks the corresponding output. This suite will attempt to use your `run.sh` and is fairly tolerant of different runtime environments. Of course, there are many aspects (e.g. clean code, documentation) that cannot be tested by our suite, so each submission will also be reviewed manually by a data engineer.

### How long will it take for me to hear back from you about my submission?
We receive hundreds of submissions and try to evaluate them all in a timely manner. We try to get back to all applicants **within two or three weeks** of submission, but if you have a specific deadline that requires expedited review, please email us at `cc@insightdataengineering.com`.


Happy coding!


