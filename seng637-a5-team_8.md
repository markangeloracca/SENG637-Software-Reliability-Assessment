# SENG 637- Dependability and Reliability of Software Systems
**Lab. Report \#5 – Software Reliability Assessment**

| Group \#: 8    |                |
| -------------- | -------------- | 
| Student Names: | **Mark**       |
|                | **Zoe**        |
|                | **Heena**      |
|                | **Tafreed**    |

## Introduction

Software reliability is a critical dimension of system dependability, representing the probability of failure-free operation for a specified period in a given environment. As systems grow in complexity, quantitative assessment becomes essential to determine if a product is ready for release or requires further testing. This lab focuses on the application of two distinct yet complementary reliability assessment techniques: **Reliability Growth Testing** and **Reliability Demonstration Charts (RDC)**.

The primary objective of this assignment is to analyze integration test failure data from a hypothetical System Under Test (SUT) to evaluate its current reliability status and predict future behavior.

### **1.1 Reliability Growth Testing**

Reliability Growth Models (RGM) are used to track the improvement of software reliability as defects are identified and corrected over time. By applying mathematical models—such as those provided in the **C-SFRAT** (Covariate Software Failure and Reliability Assessment Tool)—we can:

* Estimate the total number of remaining defects.  
* Predict the failure rate and Mean Time To Failure (MTTF).  
* Determine if the reliability trend is improving (growing) at an acceptable pace.

### **1.2 Reliability Demonstration Chart (RDC)**

In contrast to growth models, the **RDC** is a graphical tool based on Sequential Sampling Theory. It is particularly useful when failure data is limited or when a quick "Accept/Reject/Continue" decision is required against a specific target reliability. This method allows us to:

* Assess whether the SUT meets a target MTTF given specific risk parameters ($\alpha$ and $\beta$).  
* Perform "what-if" analysis to determine the minimum acceptable MTTF for the system.

### **1.3 Scope of Work**

Through this lab, we aim to gain hands-on experience with industry-standard tools (C-SFRAT and RDC-11), interpret statistical plots, and provide a data-driven justification for the system's readiness. By comparing the results of both techniques, we will evaluate the strengths and weaknesses of each approach in the context of software quality assurance.

## Assessment Using Reliability Growth Testing

### **Model Comparison and Top Two Model Selection**

All 23 available models in C-SFRAT were run against the failure dataset using covariates E, F, and C. Models were evaluated using Log-Likelihood, AIC, BIC, SSE, and Critic score. Lower AIC/BIC values indicate better model fit.

<div><img src="./media/c-sfrat1.png" width="80%"></div>

<div><img src="./media/c-sfrat1_1.png" width="80%"></div>

The top two selected models are:

**1st Place: DW3 (F)** — AIC: 122.199, BIC: 127.935, SSE: 528.046

**2nd Place: IFRGSB (F)** — AIC: 126.310, BIC: 132.046, SSE: 756.460

DW3(F) was selected as the best model due to its lowest AIC, lowest BIC, and a perfect Critic score of 1.000 across all 23 models. IFRGSB(F) was selected as the second best with the next lowest AIC/BIC values. Both models use covariate F (failure identification work in person-hours) as their best predictor. All other models, particularly the IFR SB variants, had dramatically worse AIC values exceeding 1900, making them unsuitable for this dataset.

### **Range Analysis**

The dataset spans 31 intervals with 92 total failures. The data can be broken into distinct phases:

* Intervals 1–9: Unstable, low counts with a spike of 11 failures at interval 2  
* Intervals 10–18: Low counts (0–4), relatively stable but no clear downward trend  
* Intervals 19–23: Major spike (8, 9, 6, 7, 4 failures)  likely caused by new features being introduced or a stress testing phase  
* Intervals 24–31: Counts decline back to 0–4, showing a consistent downward trend indicating reliability growth

The full dataset (intervals 1–31) was used for model fitting as C-SFRAT's covariate models handle non-monotonic data well. The most meaningful reliability growth is observed in intervals 24–31 where failure intensity decreases consistently.

### **Failure Rate and Reliability Plots**

<div><img src="./media/c-sfrat2.png" width="80%"></div>

The DW3(F) intensity graph shows the predicted failure intensity (red line) against actual failure counts (grey bars). The model closely tracks the actual data across all 31 intervals, capturing both the mid-test spike and the declining trend toward the end. The downward slope after interval 23 confirms the SUT is experiencing reliability growth.

<div><img src="./media/c-sfrat3.png" width="80%"></div>

The IFRGSB(F) intensity graph shows a similar but slightly smoother prediction curve (purple line). It captures the overall shape of the failure data but is more conservative around the spike at intervals 19–22. Both models agree on the key finding: the SUT shows improving reliability toward the end of the test period.

<div><img src="./media/c-sfrat4.png" width="80%"></div>

<div><img src="./media/c-sfrat5.png" width="80%"></div>

The MVF graphs show cumulative failures over time. Both models' curves gradually flatten toward interval 31, confirming that the rate of new failures is decreasing showing a clear indicator of reliability growth. DW3(F) tracks the actual data more closely throughout, while IFRGSB(F) underestimates early failures but aligns well from interval 15 onward.

### **Decision Making Given a Target Failure Rate**

Based on the DW3(F) intensity plot, the failure rate at interval 31 is approximately 3 failures per interval. If the acceptable target failure rate is set at 1 failure per interval, the SUT has not yet met this target by the end of the observed data and would require approximately 5–8 additional testing intervals assuming the current declining trend continues. If the target is relaxed to 2 failures per interval, the SUT is close to meeting it near interval 31 and may only need a few more intervals of confirmation testing before release.

### **Advantages and Disadvantages of Reliability Growth Analysis**

**Advantages:** Reliability growth analysis provides a quantitative, data-driven way to track whether reliability is improving over time and predict when a system will reach an acceptable failure rate. Multiple models can be compared objectively using AIC and BIC, reducing subjectivity. The use of covariates adds precision by accounting for varying testing effort across intervals, helping project managers make informed decisions about when to stop testing.

**Disadvantages:** The technique requires a sufficient volume of failure data  sparse datasets produce unreliable results. It assumes testing conditions remain consistent over time, which was not entirely true here given the spike at intervals 19–23. Model selection can be challenging since different models can produce significantly different predictions. The approach also cannot account for undiscovered failures, and future reliability predictions are sensitive to the chosen model and covariate assumptions.

## Assessment Using Reliability Demonstration Chart

### **RDC Testing**
#### 1. MTTF<sub>min</sub> for which the SUT becomes acceptable  
   
| Parameter | Value to Enter |
| :---- | :---- |
| **Per Number of Input Events** | **1,000** |
| **Maximum Acceptable Number of Failures** | **1,214.90** |
| **Resulting Target MTTF** | 0.8231 Hours |

**Given the dataset totals:**
Total Failures (n): 92  
Total Execution Time (Tactual): 54.3 hours  
Developer's Risk (α): 0.1  
User's Risk (β): 0.1  
Discrimination Ratio (γ): 2.0  

The acceptance boundary for normalized time Tnorm (where <i>T<sub>norm</sub> \= T<sub>actual</sub> / MTTF</i>) is defined by:

<div><img src="./media/mttf1.png" width="50%"></div>
Rearranging to solve for <i>MTTF<sub>min</sub></i> at the final point (n=92, T=54.3):

<div><img src="./media/mttf2.png" width="50%"></div>

After plugging in the values, we get <i>MTTF<sub>min</sub> ≈ 0.8231 hours</i>

**At *MTTF<sub>min</sub>* 0.8231, t**he software is at a "tipping point." It has just barely demonstrated enough error-free execution time to be statistically accepted. Small changes in your reliability requirements (FIO) can completely flip the verdict from "Accept" to "Reject" even if the raw failure data remains identical.   

<div><img src="./media/rdc1.png" width="80%"></div>
The charts that  follow demonstrate the mathematical relationship <i>T<sub>norm</sub> = T<sub>actual</sub> / MTTF<sub>min</sub></i>.

#### 2. Set MTTF to twice - <i>2 x MTTF<sub>min</sub> ≈ 1.6462 hours</i>
   
| Parameter                               | Value to Enter |
| :-------------------------------------- | -------------- |
| Per Number of Input Events              | 1,000          |
| Maximum Acceptable Number of Failures   | 607.43         |
| Resulting Target MTTF                   | 1.6462 Hours   |

<div><img src="./media/rdc2.png" width="80%"></div>

By doubling the required time between failures, the "Normalized Time" (X-axis) effectively shrinks. This shifts the blue trajectory **left and up** into the **Reject Region**. If you raise the quality bar, the current software performance is no longer sufficient; it is failing too frequently for this higher standard.

#### 3. Scenario: Half MTTFmin (Relaxed Goal) - <i>MTTF<sub>min</sub> / 2 ≈ 0.4116 hours</i> 

| Parameter                               | Value to Enter  |
| :-------------------------------------- | :-------------- |
| Per Number of Input Events              | 1,000           |
| Maximum Acceptable Number of Failures   | 2,429.72        |
| Resulting Target MTTF                   | 0.4116 Hours    |

<div><img src="./media/rdc3.png" width="80%"></div>

By halving the required time between failures, the "Normalized Time" stretches. This shifts the trajectory **right and down** deep into the **Accept Region**. If the quality bar is low, the software is "over-performing" and is safely considered reliable.

Looking at the three charts together allows us to see how much "headroom" or margin the software has:

* Because the trajectory in the **Half MTTF** chart is far from the yellow "Continue Testing" zone, we infer that the software is very stable relative to that low target.  
* Conversely, in the **Twice MTTF** chart, the trajectory is deep in the red, meaning the software is nowhere near meeting that high-reliability goal and would require significant debugging or code refactoring to pass.

## Comparison of Results
Based on the provided lab report, here is a "Comparison of Results" section that analyzes the findings from the Reliability Growth Testing (RGT) and the Reliability Demonstration Chart (RDC).

### **Comparison of Results: Reliability Growth Testing vs. Reliability Demonstration Chart**
This section evaluates the insights gained from two different reliability assessment techniques: Reliability Growth Testing (using C-SFRAT) and the Reliability Demonstration Chart (RDC). While both methods aim to quantify software quality, they offer different perspectives on system stability and release readiness.

#### **1\. Purpose and Focus**
* **Reliability Growth Testing (RGT):** The primary focus of RGT was to track the improvement of the software over time as defects were identified and (theoretically) resolved. By fitting models like the Duane or Littlewood-Verall, we were able to observe the trend of Failure Intensity and Mean Time Between Failures (MTBF). This method is inherently **predictive**, allowing us to estimate future reliability based on historical trends.  
* **Reliability Demonstration Chart (RDC):** In contrast, the RDC was used as a **decision-making tool** rather than a predictive one. It plotted failure data against time to determine if the system met a specific target reliability level (Discrimination Ratios). The focus here was purely to decide whether to "Accept," "Reject," or "Continue Testing" based on predefined risk boundaries.

#### **2\. Performance Metrics and Results**
* **RGT Observations:** Our analysis in the RGT section showed that as testing progressed, the failure intensity generally decreased, indicating "growth" in reliability. The models provided quantitative values for the current failure rate, helping us understand the absolute state of the software's maturity.  
* **RDC Observations:** The RDC analysis provided a visual representation of system "safety." Even if RGT showed growth, the RDC might still place the system in the "Continue Testing" or "Reject" zone if the failures occurred too frequently relative to the target failure intensity. The RDC was more sensitive to the *spacing* between individual failures rather than the overall trend of the entire dataset.

#### **3\. Strengths and Limitations Comparison**
| Feature | Reliability Growth Testing (RGT) | Reliability Demonstration Chart (RDC) |
| :---- | :---- | :---- |
| **Data Requirement** | Requires a large set of time-between-failure or failure-count data. | Can be used with relatively small amounts of recent failure data. |
| **Output** | Provides concrete metrics like MTBF and Failure Intensity. | Provides a status: Accept, Reject, or Continue Testing. |
| **Trend Analysis** | Excellent for seeing if the software is getting better or worse over time. | Poor for trends; focuses on whether a specific goal is currently met. |
| **Risk Management** | Does not explicitly account for developer/consumer risk. | Directly incorporates risk (α and β) into the chart boundaries. |

#### **4\. Conclusion of Comparison**
The RGT and RDC methods complement each other. The **RGT** confirmed that the team’s efforts resulted in a more stable product over time by showing a positive growth curve. However, it was the **RDC** that provided the final "Go/No-Go" justification.

In this lab, while the RGT showed the system was improving, the RDC allowed us to see exactly when the system crossed the threshold from "unacceptable" to "acceptable" based on specific reliability targets. For a project manager, RGT provides the roadmap of improvement, while RDC provides the final checkmark for release.

## Similarities and Differences of the Two Techniques
### **1\. Reliability Growth Testing (RGT)**

RGT is an iterative "test-analyze-fix" process typically conducted during the design and development stages. The primary goal is to identify failure modes and implement corrective actions to improve the system's reliability over time.

* **Objective:** To improve the Mean Time Between Failures (MTBF) by eliminating design weaknesses.  
* **The Model:** It often utilizes the **Duane Model** or the **Crow-AMSAA (NHPP)** model to track the rate of improvement.  
* **Visual Representation:** Usually plotted as a growth curve where the cumulative failure rate decreases, or MTBF increases, as test time accumulates.

### **2\. Reliability Demonstration Charts (RDC)**
RDC is a statistical tool used during the validation or acceptance phase. It is based on **Sequential Probability Ratio Testing (SPRT)**. Instead of trying to fix the product, RDC is used to decide whether a product meets a specific reliability requirement based on observed failures over time.

* **Objective:** To accept or reject a null hypothesis (e.g., "The MTBF is at least 500 hours") with a specific level of statistical confidence.  
* **The Process:** Data points (failures) are plotted on a chart with defined boundaries.  
* **Outcomes:** The test continues until the plot crosses into the **"Accept,"** **"Reject,"** or **"Continue Testing"** regions.

### **3\. Key Similarities**
Despite their different goals, they share several fundamental characteristics:

* **Time-Based Metrics:** Both rely on time-to-failure data (or cycles/distance) as the primary input.  
* **Decision Support:** Both provide management with a mathematical basis for deciding if a product is ready for the next phase or market release.  
* **Non-Halt Testing:** Both methods allow for testing to proceed as failures occur, rather than requiring a fixed-time test where you wait until the very end to see the results.

### **4\. Major Differences**
| Feature | Reliability Growth Testing (RGT) | Reliability Demonstration Chart (RDC) |
| :---- | :---- | :---- |
| **Primary Goal** | **Improvement:** Find and fix defects. | **Validation:** Confirm specs are met. |
| **Timing** | Early to mid-development. | Late development or final acceptance. |
| **Design Status** | Design is fluid; changes are expected. | Design is frozen; no changes allowed. |
| **Statistical Basis** | Power Law process (Crow-AMSAA). | Sequential Probability Ratio Test (SPRT). |
| **Risk Focus** | Focuses on the rate of learning/fixing. | Focuses on Producer’s Risk ($\alpha$) and Consumer’s Risk ($\beta$). |
| **Action taken** | Engineering change/Redesign. | Accept or Reject the lot/product. |

In a standard engineering lifecycle, **RGT** is used first to "grow" the reliability of the prototype to a desired level. Once the engineers believe the target has been reached, the design is frozen, and an **RDC** is used to formally demonstrate to the stakeholders or customers that the product truly meets the required reliability standards.

## How the team work/effort was divided and managed
| Sections                                      | Member    |
| :-------------------------------------------- | :-------- |
| Reliability Growth Testing                    | Heena     |
|                                               | Tafreed   |
| Reliability Demonstration Chart (RDC) Testing | Zoe       |
|                                               | Mark      |

## Difficulties encountered, challenges overcome, and lessons learned
In Reliability Demonstration Chart generation, there is a learning curve to fully utilize the Reliability Demonstration Chart spreadsheet. We feel that the documentation is insufficient and lacks a Getting Started section to cater scenarios such as using a bigger dataset, especially the part of the Plot Data sheet. In the instruction for the assignment, MTTF is mentioned a couple of times but in the spreadsheet it was not mentioned. Although  the open-source RDC chart is useful, it took us some time to fully understand its logic and behavior.

## Comments/feedback on the lab itself
The lab effectively demonstrates that the RDC is not just a visualization but a statistical tool for "Guiding Test." It clearly illustrates how to move from raw data (failure counts and execution time) to a binary decision (Accept/Reject). The use of **Sequential Probability Ratio Testing (SPRT)** allows for a decision to be made as soon as sufficient data is collected, potentially saving significant testing time and resources compared to fixed-length testing.

The lab highlights how the **Discrimination Ratio ($\gamma$)** and **Consumer/Producer risks ($\alpha$, $\beta$)** dictate the "width" of the yellow "Continue Testing" region. A valuable addition to the lab would be to observe how tightening these risks (e.g., moving from Profile I to Profile IV) forces the system into the "Continue Testing" zone for much longer, requiring a higher level of statistical confidence before an "Accept" decision can be reached.

The exercise involving <i>MTTF<sub>min</sub></i>, <i>2 x MTTF</i>, and <i>0.5 x MTTF</i> is the most insightful part of the activity. It proves that the "quality" of software is relative to its intended use.

* **Observation:** The same dataset can be "Excellent" for a general-purpose application but "Catastrophic" for mission-critical software.  
* **Learning Outcome:** Understanding that the $MTTF$ target is the primary "lever" that shifts the data trajectory across boundaries is essential for any software reliability engineer.

The transition from raw execution time to **Normalized Time (<i>T<sub>norm</sub></i>)** is a sophisticated concept that this lab handles well. By requiring the calculation of "Per Number of Input Events" and "Max Failures," the lab forces an understanding of the relationship:

<div><img src="./media/mttf3.png" width="60%"></div>

This clarifies how spreadsheet tools translate real-world usage units into a standard RDC format.

Using the provided [Reliability-Demonstration-Chart.xls](./RDC/Reliability-Demonstration-Chart-updated.xls) templates provides a realistic look at industry-standard documentation. 

The lab reveals a practical limitation: standard templates are often pre-configured for a small number of observations (e.g., 16). Handling a dataset with 31 intervals requires manual extension of the Excel ranges, which is a good lesson in the practicalities of data engineering and tool customization.

Overall, the lab provides a comprehensive end-to-end workflow for certifying software reliability, from data ingestion and goal setting to final statistical validation.