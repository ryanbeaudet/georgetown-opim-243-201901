# "Robo Advisor" Project

## Prerequisites

  + [Dictionaries](/notes/python/datatypes/dictionaries.md)
  + [Web Requests Exercise](/exercises/web-requests.md)
  + [APIs](/notes/apis.md)
  + [Environment Variables](/notes/environment-variables.md)

## Business Prompt

![a person high-fiving a robot while money rains down from the sky](https://user-images.githubusercontent.com/1328807/52987309-8e933a80-33c8-11e9-9839-766025306633.jpg)
Assume you own and operate a financial planning business which helps customers make investment decisions.

Your objective is to build yourself a tool to automate the process of providing your clients with stock trading recommendations.

Specifically, the system should accept one or more stock or cryptocurrency symbols as information inputs, then it should request real live historical trading data from the Internet, and finally it should provide a recommendation as to whether or not the client should purchase the given stocks or cryptocurrencies.



## Learning Objectives

  1. Create a tool to automate manual efforts and streamline business processes.
  2. Gain familiarity with APIs and web services, and practice issuing HTTP requests in Python (focusing on GET requests).
  3. Increase exposure to JSON-formatted data, and practice processing it in Python.
  4. Leverage built-in Python modules and third-party Python packages to speed development and enhance capabilities.

## Requirements

### Repository Requirements

Your project repository should contain an "app" directory with a "robo_advisor.py" file inside (i.e. "app/robo_advisor.py").

Your project repository should contain a "README.md" file. The README file should provide instructions to help someone else install, setup, and run your program. This includes instructions for installing package dependencies, for example using Pip. It also includes instructions for setting an environment variable named `ALPHAVANTAGE_API_KEY` (see "Security Requirements" section below).

If your desired approach to setting environment variables uses a ".env" file (see "Security Requirements" section below), your project repository should contain a file called ".gitignore" with the following contents inside to prevent that file from being tracked in version control:

```
# .gitignore

# ignore secret environment variable values
.env
```

Finally, your project repository should contain a "data" directory with another ".gitignore" file inside, with the following contents in it to ignore CSV files stored inside the data directory:

```
# data/.gitignore

# h/t: https://stackoverflow.com/a/5581995/670433

# ignore all files in this directory ...
*

# ... except this gitignore file
!.gitignore
```

### Security Requirements

Your program will need an API Key to issue requests to the [AlphaVantage API](https://www.alphavantage.co). But the program's source code should **absolutely not** include the secret API Key value.

Instead, you should set an environment variable called `ALPHAVANTAGE_API_KEY`, and your program should read the API Key from this environment variable at run-time.

You are encouraged to use a "dotenv" approach to setting project-specific environment variables by using a file called ".env" in conjunction with [the `dotenv` package](/notes/python/packages/dotenv.md). If you do, the ".env" file should **absolutely not** be tracked in version control or included in your GitHub repository.

> HINT: you may need to use a [local ".gitignore" file](https://help.github.com/articles/ignoring-files/#create-a-local-gitignore) (see "Repository Requirements" section above), or just don't upload the ".env" file to GitHub.

### Functionality Requirements

Your project should adhere to the following functionality requirements, as detailed in the sections below:

  + Information Input Requirements
  + Validation Requirements
  + Information Output Requirements
  + Calculation Requirements

#### Information Input Requirements

The system should prompt the user to input one or more stock symbols (e.g. `"MSFT"`, `"AAPL"`, etc.). It may optionally allow the user to specify multiple symbols, either one-by-one or all at the same time (e.g. `"MSFT, AAPL, GOOG, AMZN"`). It may also optionally prompt the user to specify additional inputs such as risk tolerance and/or other trading preferences, as desired and applicable.

#### Validation Requirements

Before requesting data from the Internet, the system should first perform preliminary validations on user inputs. For example, it should ensure stock symbols are a reasonable amount of characters in length and not numeric in nature.

If preliminary validations are not satisfied, the system should display a friendly error message like "Oh, expecting a properly-formed stock symbol like 'MSFT'. Please try again." and stop execution.

Otherwise, if preliminary validations are satisfied, the system should proceed to issue a GET request to the [AlphaVantage API](https://www.alphavantage.co/documentation/) to retrieve corresponding stock market data.

When the system makes an HTTP request for that stock symbol's trading data, if the stock symbol is not found or if there is an error message returned by the API server, the system should display a friendly error message like "Sorry, couldn't find any trading data for that stock symbol", and it should stop program execution, optionally prompting the user to try again.

#### Information Output Requirements

After receiving a successful API response, the system should write historical stock prices to one or more CSV files located in the repository's "data" directory. The CSV file contents should resemble the following example:

    timestamp, open, high, low, close, volume
    2018-06-04, 101.2600, 101.8600, 100.8510, 101.6700, 27172988
    2018-06-01, 99.2798, 100.8600, 99.1700, 100.7900, 28655624
    2018-05-31, 99.2900, 99.9900, 98.6100, 98.8400, 34140891
    2018-05-30, 98.3100, 99.2500, 97.9100, 98.9500, 22158528

If the system processes only a single stock symbol at a time, the system may use a single CSV file named "data/prices.csv", or it may use multiple CSV files, each with a name corresponding to the given stock symbol (e.g. "data/prices_msft.csv, "prices_aapl.csv", etc.). If the system processes multiple stock symbols at a time, it should use multiple files, each with a name corresponding to the given stock symbol (e.g. "data/prices_msft.csv", "prices_aapl.csv", etc.). If using more than one CSV file, the program should have a way of cleaning-up to prevent uncontrolled proliferation of new files.

After writing historical data to a CSV file, the system should perform calculations (see "Calculation Requirements" section below) to produce/print the following outputs:

  + The **selected stock symbol(s)** (e.g. "Stock: MSFT")
  + The **date and time when the program was executed**, formatted in a human-friendly way (e.g. "Run at: 11:52pm on June 5th, 2018")
  + The **date when the data was last refreshed**, usually the same as the latest available day of daily trading data (e.g. "Latest Data from: June 4th, 2018")
  + For each stock symbol: its **latest closing price**, its **recent high price**, and its **recent low price**, calculated according to the instructions below, and formatted as currency with a dollar sign and two decimal places with a thousands separator as applicable (e.g. "Recent High: $1,234.56", etc.)
  + A **recommendation** as to whether or not the client should buy the stock (see guidance below), and optionally what quantity to purchase. The nature of the recommendation for each symbol can be binary (e.g. "Buy" or "No Buy"), qualitative (e.g. a "Low", "Medium", or "High" level of confidence), or quantitative (i.e. some numeric rating scale).
  + A **recommendation explanation**, describing in a human-friendly way the reason why the program produced the recommendation it did (e.g. "because the stock's latest closing price is exceeds threshold XYZ")

> NOTE: the CSV files are information outputs of this system, not information inputs. So it shouldn't be necessary for your program to read a CSV file to perform calculations. The JSON API responses should have all the information your program needs to perform calculations.

#### Calculation Requirements

The **latest closing price** should be equal to the stock's "close" price on the latest available day of trading data.

The **recent high price** should be equal to the maximum daily "high" price over approximately the past 100 available days of trading data.

The **recent low price** should be calculated in a similar manner as the **recent high price**, but it should instead be equal to the minimum of all daily "low" prices.

> NOTE: By default, the [daily data returned by the AlphaVantage API](https://www.alphavantage.co/documentation/#daily) uses an `outputsize` parameter value of `compact`. This "compact" response should provide daily data covering the previous 100 trading days, which is sufficient to use to calculate the **recent high** and **recent low** prices. It is acceptable and recommended to use these default, "compact" responses to calculate these recent prices.

You are free to develop your own custom **recommendation** algorithm. This is perhaps one of the most fun and creative parts of this project. :smiley: One simple example algorithm would be (in pseudocode): If the stock's latest closing price is less than 20% above its recent low, "Buy", else "Don't Buy".














## Setup

### From Starter

To setup your project repository, either fork and clone the professor's ["Robo Advisor" Starter Repository](https://github.com/prof-rossetti/robo-advisor-starter-py), or follow the steps below to setup your own from scratch.

### From Scratch

Take this time to create a new repository on GitHub.com called something like "stock-rec-system" or "robo-advisor-project". We'll refer to this as your "remote project repository".

Clone or download the remote project repository onto your local machine, perhaps on your Desktop. We'll refer to this as your "local project repository".

Navigate to your local project repository from the command-line.

Within the local project repository, create a new directory called "app" with a "robo_advisor.py" file inside, to include the following contents:

```py
# app/robo_advisor.py

# TODO: import some modules and/or packages here

# TODO: write some Python code here to produce the desired functionality...

print("-----------------------)
print("STOCK SYMBOL: AMZN")

print("-----------------------)
print("CRUNCHING THE DATA...")

print("-----------------------)
print("LATEST CLOSING PRICE: $1,259.19")

# ... etc.
```

Create a new virtual environment named something like "stocks-env" and activate it. Then from inside the virtual environment, execute the Python script to see it print the provided contents.

Finally, make your first commit with a message like "Setup project repository", and push these changes to GitHub. Once you see these changes reflected in your remote project repository on GitHub.com, you are ready to start the project development process.




## Development

As you develop your project repository, incrementally "commit" your work along the way. By the time you are finished with development, your project repository should contain a version history including at least a handful of incremental commits.

### Further Exploration

If you're able to complete the basic project requirements with relative ease, consider addressing one or more of the ["Robo Advisor" Further Exploration Challenges](robo-advisor/further.md) (e.g. processing multiple stocks, visualizing the stock price over time, etc.).

## Submission

To submit your project:

  1. Push your local project repository to GitHub, so you can visit your remote project repository at a URL like `https://github.com/YOUR_USERNAME/robo-advisor-project`.
  2. Fork the ["upstream" course repository](https://github.com/prof-rossetti/georgetown-opim-243-201901) (or refresh your existing fork).
  3. Update your forked course repository's ["Robo Advisor" Submissions CSV file](robo-advisor/submissions.csv).
to include your GitHub username and your project repository's URL.
  4. Submit a Pull Request for your forked course repository's changes to be accepted into the "upstream" course repository.


## Evaluation

Project submissions will be evaluated according to the requirements set forth above, as summarized by the rubric below:

Category | Requirement | Weight
--- | --- | ---
Repository | Includes README.md file with detailed instructions | 7.5%
Security | Excludes secret API Key values from the source code | 12.5%
Validations (Prelim) | Prevents an HTTP request if stock symbol not likely to be valid (e.g. `8888`) | 5%
Validations | Fails gracefully if encountering a response error | 7.5%
Calculations | Displays accurate information | 15%
Info Outputs | Displays final recommendation, including justification / context | 17.5%
Info Outputs | Writes historical prices to CSV file | 10%
Info Outputs | Formats all prices as USD (doesn't apply to CSV file values) | 5%
Dev Process | Submitted via remote Git repository which reflects an incremental revision history | 20%

This rubric is tentative, and may be subject to slight adjustments during the grading process.

If experiencing execution error(s) while evaluating the application's required functionality, evaluators are advised to reduce the project's grade by anywhere between 4% and 50%, depending on the circumstances and severity of the error(s).

In recognition of deliverables which exhibit functionality above and beyond the basic required functionality, including "further exploration" challenges, evaluators are encouraged to award between 0.5% and 5.0% extra credit "engagement points" to be applied towards the final exam.
