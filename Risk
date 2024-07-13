import streamlit as st
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Function to calculate NPV
def calculate_npv(fcff, r):
    return sum([fcff[i] / (1 + r) ** i for i in range(len(fcff))])

# Monte Carlo simulation for NPV
def monte_carlo_simulation(fcff, r_mean, r_std, num_simulations):
    npvs = []
    for _ in range(num_simulations):
        r = np.random.normal(r_mean, r_std)
        npv = calculate_npv(fcff, r)
        npvs.append(npv)
    return npvs

# Streamlit app
st.title("NPV Risk Analysis")

# Inputs for FCFF for each year
st.header("Input Free Cash Flow to Firm (FCFF) for each year")
fcff = []
num_years = st.number_input("Number of Years", value=5, min_value=1)
for i in range(num_years):
    fcff.append(st.number_input(f"Year {i + 1} FCFF", value=0.0))

# Input for base discount rate
st.header("Input Discount Rate")
base_discount_rate = st.number_input("Base Discount Rate", value=0.1, format="%.5f")

# Sensitivity Analysis
st.header("Sensitivity Analysis")
sensitivity_variable = st.selectbox("Variable to Analyze", ["FCFF", "Discount Rate"])
sensitivity_range = st.slider("Sensitivity Range (%)", min_value=-50, max_value=50, value=(-10, 10), step=1)

# Scenario Analysis
st.header("Scenario Analysis")
best_case = st.number_input("Best Case Discount Rate", value=0.08, format="%.5f")
worst_case = st.number_input("Worst Case Discount Rate", value=0.12, format="%.5f")

# Monte Carlo Simulation
st.header("Monte Carlo Simulation")
r_mean = st.number_input("Mean Discount Rate for Monte Carlo Simulation", value=0.1, format="%.5f")
r_std = st.number_input("Standard Deviation of Discount Rate for Monte Carlo Simulation", value=0.02, format="%.5f")
num_simulations = st.number_input("Number of Simulations", value=1000, min_value=100, step=100)

# Perform Sensitivity Analysis
if st.button("Run Sensitivity Analysis"):
    sensitivity_results = []

    if sensitivity_variable == "FCFF":
        for change in range(sensitivity_range[0], sensitivity_range[1] + 1):
            modified_fcff = [f * (1 + change / 100) for f in fcff]
            npv = calculate_npv(modified_fcff, base_discount_rate)
            sensitivity_results.append((change, npv))
    elif sensitivity_variable == "Discount Rate":
        for change in range(sensitivity_range[0], sensitivity_range[1] + 1):
            modified_discount_rate = base_discount_rate * (1 + change / 100)
            npv = calculate_npv(fcff, modified_discount_rate)
            sensitivity_results.append((change, npv))

    sensitivity_df = pd.DataFrame(sensitivity_results, columns=["Change (%)", "NPV"])
    
    # Display Sensitivity Analysis Results
    st.subheader("Sensitivity Analysis Results")
    st.dataframe(sensitivity_df)
    
    # Plot Sensitivity Analysis Results
    plt.figure(figsize=(10, 6))
    plt.plot(sensitivity_df["Change (%)"], sensitivity_df["NPV"], marker='o')
    plt.xlabel("Change (%)")
    plt.ylabel("NPV")
    plt.title(f"Sensitivity Analysis of {sensitivity_variable}")
    plt.grid(True)
    st.pyplot(plt.gcf())

# Perform Scenario Analysis
if st.button("Run Scenario Analysis"):
    base_npv = calculate_npv(fcff, base_discount_rate)
    best_case_npv = calculate_npv(fcff, best_case)
    worst_case_npv = calculate_npv(fcff, worst_case)
    
    scenario_results = {
        "Scenario": ["Worst Case", "Base Case", "Best Case"],
        "Discount Rate": [worst_case, base_discount_rate, best_case],
        "NPV": [worst_case_npv, base_npv, best_case_npv]
    }
    
    scenario_df = pd.DataFrame(scenario_results)
    
    # Display Scenario Analysis Results
    st.subheader("Scenario Analysis Results")
    st.dataframe(scenario_df)
    
    # Plot Scenario Analysis Results
    plt.figure(figsize=(10, 6))
    plt.bar(scenario_df["Scenario"], scenario_df["NPV"], color=["red", "blue", "green"])
    plt.xlabel("Scenario")
    plt.ylabel("NPV")
    plt.title("Scenario Analysis of NPV")
    plt.grid(True)
    st.pyplot(plt.gcf())

# Perform Monte Carlo Simulation
if st.button("Run Monte Carlo Simulation"):
    npvs = monte_carlo_simulation(fcff, r_mean, r_std, num_simulations)
    
    # Display simulation results
    st.subheader("Monte Carlo Simulation Results")
    st.write(f"Mean NPV: ${np.mean(npvs):,.2f}")
    st.write(f"Standard Deviation of NPV: ${np.std(npvs):,.2f}")
    st.write(f"5th Percentile NPV: ${np.percentile(npvs, 5):,.2f}")
    st.write(f"95th Percentile NPV: ${np.percentile(npvs, 95):,.2f}")
    
    # Plot histogram of NPV results
    plt.figure(figsize=(10, 6))
    plt.hist(npvs, bins=50, edgecolor='k', alpha=0.7)
    plt.axvline(np.mean(npvs), color='r', linestyle='dashed', linewidth=2)
    plt.xlabel('NPV')
    plt.ylabel('Frequency')
    plt.title('NPV Distribution from Monte Carlo Simulation')
    st.pyplot(plt.gcf())
