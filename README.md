To understand the risks associated with user-controllable inputs that drive operation logic and to systematically investigate their semantic associations within interface operations, we conducted an empirical study. We define operation logic as the functionality within an interface that triggers persistent state changes or alters the device's service control flow. The user-controllable inputs that drive this logic are henceforth referred to as core parameters.



To ensure the accuracy of our analysis, we excluded CVE reports with broken links that could not be reproduced. We collected 477 reproducible reports from the historical CVEs of mainstream vendors, including Netgear, D-Link, Linksys, Tenda, TOTOLINK, and TP-Link, forming our benchmark dataset. The complete list of CVEs can be found in Investigation/CVE.xlsx.



**Table 1: Results of Expert Review on Semantic Identification in Black-box Scenarios.**

| **Evaluation Metric** | **Count** | **Ratio (%)** | **Description**                           |
| --------------------- | --------- | ------------- | ----------------------------------------- |
| True Positive (TP)    | 332       | 86.01%        | Core parameters correctly identified      |
| False Negative (FN)   | 54        | 13.99%        | Core parameters missed by experts         |
| True Negative (TN)    | 74        | 81.32%        | Non-core parameters correctly excluded    |
| False Positive (FP)   | 17        | 18.68%        | Non-core parameters misidentified as core |
| **Precision**         | **-**     | **95.13%**    | TP/(TP+FP)                                |
| **F1-Score**          | **-**     | **90.34%**    | Harmonic mean of Precision and Recall     |



To demonstrate that core parameters pose a higher security risk, we reproduced and analyzed the vulnerabilities in the benchmark dataset. We utilized reverse engineering to examine the execution of parameters in the back-end code and determine whether they qualify as core parameters. Simultaneously, we performed random sampling of other parameters from the same interfaces (which had no identified vulnerabilities) to serve as a control group. For the 477 parameters in the control group, we likewise verified their execution via reverse engineering.



The results show that the triggering parameters in 386 vulnerabilities (80.92%) were identified as core parameters, whereas this was the case for only 135 (28.30%) in the control group. A chi-square test confirms that this difference is statistically significant ($\chi^2 = 266.4$, $p < 0.001$). These findings indicate that core parameters are significantly over-represented in vulnerabilities. The reason is that core parameters drive operations that trigger persistent device state changes or alter service control flow. Inherently, these functions involve sensitive sinks (e.g., memory operations and command executions). Consequently, IoT vulnerabilities are primarily triggered by parameters flowing into these sensitive sinks. Therefore, prioritizing core parameters during fuzzing significantly increases the probability of discovering vulnerabilities and improves overall testing efficiency.



To evaluate the feasibility of identifying core parameters using operation semantics in a black-box scenario, we designed a double-anonymous expert review. We extracted ⟨Interface URI, Triggering Parameter Name⟩ tuples from the CVE descriptions in the benchmark dataset. We then invited three experts, each with over five years of experience in IoT vulnerability research, to conduct the review. To ensure objectivity, the process employed an independent, double-anonymous mechanism in which experts were unaware of one another. Experts were required to determine, solely from the provided tuples, whether a parameter name semantically indicated that it was a core parameter for the interface's operation logic. This was a binary classification task (Yes/No). Disagreements were resolved by majority vote. 



As shown in Table 1, among the 386 verified core parameters, 332 could be directly identified through naming semantics. Furthermore, the experts correctly excluded 74 non-core parameters (out of a total of 91). To ensure the objectivity of the semantic definitions, we calculated Cohen's Kappa (k=0.73), indicating substantial inter-rater agreement among the experts. Notably, experts were provided only a limited subset of observable information (interface URIs) in the black-box setting. However, more information is actually available within device front-ends and network packets. This further demonstrates the feasibility of using semantic connections to identify core parameters to guide fuzzing.



