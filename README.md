# Brent Crude Price Prediction and Geopolitical Stress Testing

## Overview
This project develops a predictive model for Brent Crude oil prices, integrating geopolitical risk and structural shock features. It leverages historical price data, sentiment analysis from news headlines, and custom-defined geopolitical scenarios to forecast prices for the next 90 days. A key feature is the stress testing framework, which simulates distinct future scenarios (De-escalation vs. Prolonged Conflict) and evaluates their impact on oil prices.

## Methodology

1.  **Data Acquisition**: Daily closing prices for Brent Crude (BZ=F) and WTI (CL=F) from January 2024 to April 20, 2026, were downloaded using the `yfinance` library.

2.  **Data Cleaning and Preparation**: Missing values were handled using forward and backward fill. A rolling median approach was used to identify and smooth out any significant price spikes. A 50-day moving average was also calculated to visualize trends.

3.  **Geopolitical Risk Scoring**: A mock dataset of news headlines was created to simulate geopolitical events. The FinBERT (Financial Bidirectional Encoder Representations from Transformers) model was then used to perform sentiment analysis on these headlines, generating a daily 'Geopolitical Risk Score' ranging from -1 (Conflict Escalation) to 1 (Resolution).

4.  **Structural Shock Features**: Several exogenous variables, representing potential 'structural shocks' to the oil market, were added to the dataset:
    *   `Hormuz_Status`: A binary indicator (1 for potential blockade tension, 0 otherwise) for April 10-19, 2026.
    *   `OPEC_Adjustment`: A binary indicator (1 for dates on or after April 5, 2026) reflecting a 206k bpd OPEC production increase announcement.
    *   `Physical_Futures_Spread`: A constant value of $20 (as a proxy in the absence of actual futures data).
    *   `Supply_Disruption_Volume`: A constant of 13 million bpd loss (representing baseline disruptions).
    *   `Conflict_Intensity`: A new feature set to 0.2 for April 7-17, 2026 (ceasefire period) and 0.9 for April 19-20, 2026 (representing heightened conflict).

5.  **LSTM Model for Price Prediction**: A PyTorch Long Short-Term Memory (LSTM) neural network model was built. The model was trained using historical Brent Crude prices along with the geopolitical risk scores and all structural shock features as inputs. It was trained to predict the next day's Brent Crude price, and then used in a rolling forecast to predict prices for 90 days.

6.  **Stress Testing**: A `simulate_scenario` function was developed to evaluate the model's behavior under two distinct future scenarios:
    *   **Scenario A (De-escalation)**: Assumes the Strait of Hormuz reopens by May 15, 2026, leading to reduced geopolitical tension (Geopolitical Risk Score of 0.8) and no blockade.
    *   **Scenario B (Prolonged Conflict)**: Assumes infrastructure damage remains unrepaired through June 2026, implying an increased supply disruption (18 million bpd loss), heightened geopolitical risk (Geopolitical Risk Score of -0.8), and a **full Strait of Hormuz closure for the entire 90-day forecast**. This scenario also includes a **$15/bbl 'Security Premium'** on prices and a **+/- $10 high-volatility window for April 21-22, 2026** (to account for 'Pakistan Talks').

## Key Findings and Predictions

### Baseline Prediction (Next 90 Days)
*   The model predicts a general downward trend for Brent Crude prices over the next 90 days, moving from approximately $91 to around $85. This prediction includes a heuristic ±5% predicted price range.

### Stress Test Scenarios

**Scenario A: De-escalation**
*   **Impact**: Under a de-escalation scenario with the Strait of Hormuz reopening by May 15, 2026, the model forecasts a more significant and continuous decline in Brent Crude prices. Prices fall from the current levels to approximately $76 by mid-July. This highlights the strong negative impact of geopolitical de-escalation and stable supply on oil prices.

**Scenario B: Prolonged Conflict**
*   **Impact**: This scenario, incorporating prolonged infrastructure damage, a full Strait of Hormuz closure, a $15/bbl security premium, and high volatility during 'Pakistan Talks', predicts significantly higher Brent Crude prices. Prices start higher (around $106 for April 18) and remain elevated, stabilizing around $98-99 by mid-July. The specific volatility around April 21-22 is also observable, with prices potentially swinging by +/- $10.

### Visualizations
The generated plots visually compare the historical Brent Crude prices, the baseline prediction, and the distinct price trajectories for both the De-escalation and Prolonged Conflict scenarios, clearly illustrating the potential impacts of different geopolitical outcomes.

## Limitations and Future Work
*   The geopolitical risk scores are currently based on mock news headlines. Integration with real-time news APIs (e.g., NewsAPI) would significantly enhance realism.
*   The 'Physical-Futures Spread' is a constant value. Incorporating actual futures market data would improve accuracy.
*   'Supply Disruption Volume' and specific scenario parameters are user-provided estimates. Dynamic data sources for these would strengthen the model.
*   The predicted price range uses a heuristic 5% margin. Future work could involve developing a statistically derived confidence interval.

## Conclusion
This analysis provides a robust framework for Brent Crude price prediction and stress testing. By integrating geopolitical factors and simulating various scenarios, it offers valuable insights for strategic planning and risk assessment in volatile oil markets.
