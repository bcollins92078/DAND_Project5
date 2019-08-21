# Prosper Loan Data
## by Bruce Collins


## Dataset

> The dataset analyzed consists of data about 113,066 loans made on the Prosper Marketplace, a peep-to-peer lending platform, between 11/15/2005 and 03/12/2014. The dataset contains up to 81 variables for each loan including the characteristics of the loan itself (e.g., loan amount, interest rate, fees, number of investors and status of the loan at the time that the data was collected) and about the borrower (e.g., various credit scores and ratings, employment info, income, debt-to-income ratio, borrower's state and credit history info like date of first credit line and deliquencies).
>
> The dataset was downloaded in the form of a csv file from [here](https://www.google.com/url?q=https://s3.amazonaws.com/udacity-hosted-downloads/ud651/prosperLoanData.csv&sa=D&ust=1554486256021000) (link provided by Udacity). The following cleaning of the original data was performed before the exploratory data analysis:
> * Some loans appeared more than once and each case the only difference in the duplicated rows was the ProsperScore value. This was cleaned by averaging these ProsperScore values and using that value in one single row. 
> * Some of the FICO credit score values (CreditScoreRangeLower and CreditScoreRangeUpper) were outside of the valid range of 300 to 880. These values were set to NaN instead.
> * Date fields ListingCreationDate, ClosedDate, DateCreditPulled and LoanOriginationDate were converted from string format to datetime format.

## Summary of Findings

> During univariate exploration of the dataset following was uncovered:
> * The proportion of the loans that had closed when the data was collected that had LoanStatus values of either in "defaulted" or "chargedoff" was 30.9%. This extraordinarily high failure rate was in contrast to the fact that only 3.9% of the loans that were still open at the time that the data was collected had a LoanStatus of "past due" by any amount of time. Further investigation revealed that the loan performance on the Prosper Marketplace improved drastically over the 8+ years captured in the dataset.
> * The Prosper Marketplace underwent significant changes in the middle of the timespan captured in the dataset when operations were suspended in Novermber of 2008 due to a "cease and desist order" issued by the SEC. Operations resumed in July of 2009 after many policy changes had been instituted. These events are manifest in the data including significantly improved loan performance and, surprisingly, higher interest rates. Given that significant policy changes occurred when the platform relaunched service a new boolean variable was added to indicate whether or not the loan was originated after the relaunch.
> * There are four borrower creditworthiness variables in the dataset: CreditGrade, CreditScoreRangeLower/CreditScoreRangeUpper (FICO credit score), ProsperRating (Alpha & numeric) and ProsperScore. The distributions of borrowers' creditworthiness according to each of these metrics had a similar shape.
> * Loans of duration (term) 12, 36 and 60 months were available on the Prosper Marketplace. The 12 month term was only available from Q1 2011 thru Q2 2013. 77% of the loans were 36 month term and 21% were 60 month term.
> * The distribution of interest rates on loans (BorrowerRate) was concentrated in the 5% to 35% range. A comparison of the distribution of interest rates before and after relaunch shows that the interests rates shifted higher after relaunch. This in spite of the fact that interest rates in general _fell_ after the financial crisis of 2008 and the fact that credit quality of borrowers on the platform rose.
> * The distribution of loan amounts (LoanOriginalAmount) shows prominent spikes at multiples of $5000 up to the limit of $35,000. The max loan amount available on the platform was raised from $25,000 to $35,000 in 2013.
> * The dominate reasons listed by borrowers for taking a loan was Debt Consolidation. More than 50% of loans had that reason code. The next most often listed reasons were Not Available at 15% and Other at 9.5%. There were 21 reason codes to choose from.
> * Added a new variable to quantify the investors' return on loans.  InvestorReturnRatio = (LP_CustomerPayments + (LP_GrossPrincipalLoss - LP_NetPrincipalLoss) - LP_ServiceFees - LP_CollectionFees) / LoanOriginalAmount. So if the borrower repaids the loan, the InvestorReturnRatio would be one plus the effective interest rate compounded over the term of the loan, where effective interest rate is the borrower's interest rate adjusted for fees the investor pays. 
>
> During bivariate exploration of the dataset following insights were gained:
> * Explored the correlation between each of the 3 primary metrics for borrower creditworthiness in the data. I was surprised at how weak the correlation was between these variables given that they are all attempting to measure the same thing.
> * While all three of these creditworthiness variables show a similar general trend between increasing credit quality and lower risk of loan default, ProsperRating seemed to be best correlated with loan outcomes measured both by simple "troubled loan" metric and by the more rigorous investor return ratio metric.
> * The average number of investors on a given loan plotted by FICO credit score showed that investors have a strong preference for high credit score borrowers. In fact, the shape of the average number of investors per loan vs credit score looks quite similar to the shape of the investor return ratio vs credit score curve! 
> * Violin plots of loan interest rate vs creditworthiness show the expected trend of higher interest rate for lower credit quality rating.
> * LoanOriginalAmount statistics per ProsperRating category shows the range of loan amounts to be significantly lower for the lower credit quality categories. Investigation into the PercentFunded variable revealed that this lower loan amount was _not_ due to lower funding percentages.
> * Detailed look at InvestorReturnRatio across the range of each of the 3 creditworthiness variables reveals nuanced variations. The mean InvestorReturnRatio is not strictly monotonic for any of the creditworthiness variables although it is nearly so for ProsperRating. It also shows that this average InvestorReturnRatio is _less than one_ for more than half of each of the creditworthiness variable's range. This is probably due to the fact that this performance metric only applies to closed loans and these loans included in the dataset were originated when the performance on the platform was still relatively poor.
> * The InvestorReturnRatio distribution was plotted separately for successful and unsuccessful loans and the distributions were starkly, but not surprisingly, different. Successful loans have a mean InvestorReturnRatio of 1.19 while this mean for unsuccessful loans is only 0.45!
> * The plot of InvestorReturnRatio vs LoanOriginalAmount was not too revealing except that for loans of more than $25,000 the performance was outstanding! This is at least partally due to the fact that there are only a small number of closed loans in this category (17) and the previous plot of LoanOriginalAmount vs ProsperRating shows that all of these large loans went to borrowers with AA, A or B ratings.
> * Troubled loan rates plotted by IncomeRange category looked surprising similar to troubled loan rates vs ProsperRating. 
> * Stacked bar chart of IncomeRange vs ProsperRating clearly depicts the relationship between this two variables. Higher ProsperRating category have a higher income range by proportion of borrowers.

During previous exploration phases the question of fraud occurred to me. The distribution of InvestorReturnRatios shows a spike at zero return and upon a closer look I noticed a large number of loans for which there were zero payments recorded. Apparently the borrower applied for a loan on the platform, was approved and accepted the funds, but then never make any payments at all. This appears to me to be a fraudulent loan which I believe (based on my research) to be a particular concern in the online lending industry. I then recalled that the ProsperScore was designed to predict probability of a loan "going bad" within the first year and these zero-payment loans certainly fix that criteria. I wanted to see how well ProsperScore rated this subset of loans so I plotted count of these potentially fraudulent loans against ProsperScore and ProsperRating in a heatmap. It turns out that, based on this relatively small sample, ProsperScore did not do particularly well. In fact, a low ProsperRating is better correlated to these zero payment loans then the ProsperScore.
> * 


## Key Insights for Presentation

> 1. The Prosper Marketplace evolved in some very significant respects of the course of the 8+ years spanned by the dataset. These changes needed to be accounted for in the analysis in order to draw correct insights so the timeseries plots showing the prosper.com shutdown and relaunch 7 months later need to be conveyed and some of the consequences of the policy changes that accompanied the relaunch.
> 2. There are a total of 4 credit quality variables in the dataset, 3 of which are only somewhat correlated to each other. How are they related to each other and to the outcome of loans made on the platform? Which one(s) would I recommend to someone considering borrowing or investing on the platform? 
> 3. How well does the ProsperScore variable predict "zero-payment" loans, given that it was proported to have been designed to predict the probability of a loan "going bad" within the first year?

## Resources
> * prosper.com
> * lendacademy.com for articles that were helpful in understanding the peer-to-peer lending landscape and history
> * stackoverflow.com for too many instances than I can adequately recall of examples and question answers
> * obviously Pandas, Matplotlib, Seaborn, Numpy ... online documentation

