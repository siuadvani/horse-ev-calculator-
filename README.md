# horse-ev-calculator-
A simple app to calculate expected value from horse racing odds
import streamlit as st
import pandas as pd

st.set_page_config(page_title="Horse Racing EV Calculator", layout="centered")

st.title("🐎 Horse Racing EV Calculator")

st.markdown("""
This app helps you calculate **Expected Value (EV)** for each horse based on their odds.
""")

st.header("Enter the Odds")

num_horses = st.number_input("Number of horses in the race:", min_value=2, max_value=20, value=6)

odds_list = []
total_inverse_odds = 0

st.subheader("Decimal Odds (e.g., 5/1 = 6.0)")

for i in range(int(num_horses)):
    col1, col2 = st.columns(2)
    with col1:
        horse_name = st.text_input(f"Horse {i+1} name", value=f"Horse {i+1}")
    with col2:
        odds = st.number_input(f"Decimal Odds for {horse_name}", min_value=1.01, value=6.0, step=0.1)
    odds_list.append((horse_name, odds))
    total_inverse_odds += 1 / odds

if st.button("Calculate EV"):
    ev_data = []

    for horse, odds in odds_list:
        implied_prob = (1 / odds)
        fair_prob = implied_prob / total_inverse_odds
        expected_value = odds * fair_prob
        ev_data.append({
            "Horse": horse,
            "Odds": odds,
            "Implied %": round(implied_prob * 100, 2),
            "Fair %": round(fair_prob * 100, 2),
            "Expected Value": round(expected_value, 2)
        })

    df = pd.DataFrame(ev_data)
    st.subheader("📊 Results")
    st.dataframe(df.sort_values(by="Expected Value", ascending=False))

    best_bet = df.loc[df["Expected Value"].idxmax()]
    st.success(f"💡 Best Bet by EV: **{best_bet['Horse']}** with EV: **{best_bet['Expected Value']}**")
