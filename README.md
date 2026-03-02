## PhishGuard AI — Real-Time Phishing Website Detection

An intelligent AI-driven framework that analyzes website characteristics in real time and classifies URLs as phishing or legitimate using a multi-feature machine learning pipeline.

## About

PhishGuard AI is a cybersecurity tool designed to detect phishing websites before users fall victim to credential theft. Traditional defenses like browser blacklists are reactive and fail against newly registered phishing domains. This project addresses that gap by building a proactive, feature-driven classification system that extracts 30 features from three domains — URL structure, HTML content, and NLP page text — and feeds them into a Random Forest ensemble classifier. The result is a real-time REST API with a user-friendly web interface that provides not just a SAFE/SUSPICIOUS/PHISHING verdict, but a human-readable explanation of which features triggered the alert.

## Features

- Multi-domain feature extraction: URL lexical features (15), HTML structural features (12), and NLP content features (3) combined into a 30-dimensional input vector.
- Random Forest classifier with 100 estimators, balanced class weighting, and reproducible results via fixed random seed.
- Real-time Flask REST API with `/api/scan` (single URL) and `/api/batch` (up to 10 URLs) endpoints.
- Risk scoring system: probability score 0–100 mapped to SAFE (<30), SUSPICIOUS (30–65), or PHISHING (>65).
- Explainability module: `get_explanation()` returns ranked human-readable risk factors (e.g., "Password form submits to external domain — credential harvesting detected").
- Graceful fallback: if HTML fetch fails (unreachable site), URL features alone are used with zero-padded HTML/NLP features.

## Requirements

* Operating System: Windows 10/11 or Ubuntu 20.04+ (64-bit)
* Python: 3.9 or later
* Web Framework: Flask >= 2.3.0 for REST API deployment
* Machine Learning: scikit-learn >= 1.3.0 for Random Forest classifier
* Data Processing: NumPy >= 1.24.0 for feature vector operations
* Web Scraping: BeautifulSoup4 >= 4.12.0 and lxml >= 4.9.0 for HTML parsing
* HTTP Client: requests >= 2.31.0 for live URL fetching
* Model Serialization: joblib >= 1.3.0 for loading the trained .pkl model
* Development IDE: VS Code or PyCharm recommended
* Version Control: Git for collaborative development

## System Architecture

The system follows a five-stage pipeline:

**Input → Feature Extraction → Classification → Risk Scoring → Output**

1. User submits a URL via the web interface or `/api/scan` POST request
2. `feature_extractor.py` extracts URL features offline, then fetches the page to extract HTML and NLP features
3. `build_feature_vector()` assembles the ordered 30-dimensional numpy array
4. The pre-trained RandomForestClassifier outputs `predict_proba()[1]` — phishing probability
5. The Flask API returns a JSON response including risk score, verdict, top features, scan time, and explanations

## Output

#### Output 1 — Scanner Interface
The user enters a URL in the input field and clicks Scan. The interface shows a real-time loading animation while the backend fetches and analyzes the page.

#### Output 2 — Verdict & Explanation
After scanning, the system displays the risk score (0–100), verdict badge (SAFE / SUSPICIOUS / PHISHING), and a list of triggered risk factors with icons and severity levels.

Training Accuracy: ~99.7% on synthetic training data  
Note: Real-world accuracy metrics pending evaluation on UCI Phishing Dataset (11,055 labeled URLs).

## Results and Impact

PhishGuard AI demonstrates that a 30-feature multi-domain approach significantly improves detection compared to URL-only methods. The explainability layer makes the system accessible to non-technical users, helping them understand why a site was flagged rather than receiving a black-box warning. The modular architecture allows the feature extraction pipeline and classifier to be updated independently — new phishing indicators can be added as threat patterns evolve without retraining the entire model. This project serves as a foundation for a future browser extension and mobile deployment that could provide real-time phishing protection at scale.

## Articles Published / References

1. R. M. Mohammad, F. Thabtah, and L. McCluskey, "Predicting phishing websites based on self-structuring neural network," Neural Computing and Applications, vol. 25, no. 2, pp. 443–458, 2014.
2. A. Hannousse and S. Yahiouche, "Towards benchmark datasets for machine learning based website phishing detection," Engineering Applications of Artificial Intelligence, vol. 101, p. 104230, 2021.
3. G. Xiang, J. Hong, C. P. Rose, and L. Cranor, "CANTINA+: A feature-rich machine learning framework for detecting phishing web sites," ACM Transactions on Information and System Security, vol. 14, no. 2, pp. 1–28, 2011.
4. L. Breiman, "Random forests," Machine Learning, vol. 45, no. 1, pp. 5–32, 2001.
5. R. Verma and N. Hossain, "Semantic feature selection for text with application to phishing email detection," in Proc. IEEE ICMLA, 2017, pp. 1–8.
