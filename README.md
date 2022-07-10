# nvd_feeds_analysis
The Jupyter Notebook with the analysis of NVD feeds to predict software system vulnerability issue reports. It consists of three parts: extensive exploratory data analysis, predictive modeling part using LightGBM library, and results interpretation with the [SHAP tool](https://shap.readthedocs.io/en/latest/index.html). 

## Background
Common Vulnerabilities and Exposures (CVE) is a dictionary of publicly known information security vulnerabilities and exposures run by the MITRE Corporation. [U.S. National Vulnerability Database (NVD)](https://nvd.nist.gov) integrated this source of information in their feed with some added metrics. One can download the JSON file used in this analysis [here](https://nvd.nist.gov/feeds/json/cve/1.0/nvdcve-1.0-2018.json.zip). This file contains information about publicly known information from security vulnerabilities in software and hardware along with their corresponding exposures.

**Abbreviations used in the notebook**:
- CVE - Common Vulnerabilities and Exposures
- NVD - National Vulnerability Database
- CVSS - Common Vulnerability Scoring System
- CWE - Common Weakness Enumeration

**Base JSON fields used for analysis**:
- ID: unique CVE identifier
- description: text information with problem details and the affected system versions listed
- publishedDate: the date when the first occurrence of CVE was documented
- lastModifiedDate: the date when some changes on the CVE report were made 
- cvssV2_baseScore: is used to calculate the severity of the vulnerability and ranges from 0 to 10 with 10 being the highest vulnerability risk
- cvssV2_exploitabilityScore: this metric measures the current state of exploit techniques or code availability. Public availability of easy-to-use exploit code increases the number of potential attackers by including those who are unskilled, thereby increasing the severity of the vulnerability ([source](https://www.first.org/cvss/v2/guide)).
- cvssV2_impactScore: is composed of the three impact metrics which measure how a vulnerability, if exploited, will directly affect an IT asset, where the impacts are independently defined as the degree of loss of confidentiality, integrity, and availability ([source](https://www.first.org/cvss/v2/guide)).
- reference_cnt: the number of references for a given CVE
- exploit_db_flag: 1 if the CVE has at least one reference to www.exploit-db.com, 0 otherwise

**Additional JSON fields used for current analysis**:
- CWE: a software weakness, ie error that can lead to a vulnerability
- version_cnt: the number of system/OS versions affected by a given CVE


One should be aware that the Base Score is just a linear combination of Exploitability and Impact scores when building a model. Below are formulas for calculating Base, Exploitability, and Impact scores according to the NVD website:

```
BaseScore = (.6*Impact +.4*Exploitability-1.5)*f(Impact)
 
Impact = 10.41 * (1 - (1 - ConfImpact) * (1 - IntegImpact) * (1 - AvailImpact))

Exploitability = 20 * AccessComplexity * Authentication * AccessVector

f(Impact) = 0 if Impact=0; 1.176 otherwise

```

**Note**: The term "system" will be used as a generalized reference to all software and hardware products that can potentially become vulnerable to a hacker attack and are mentioned on the NVD site.
