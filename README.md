# Calibrating Stochastic and Interest Rate Models

This project demonstrates the practical implementation of advanced stochastic models used in quantitative finance for pricing equity derivatives and modeling interest rate dynamics. It covers three major components:

1. **Calibration of the Heston stochastic volatility model** to short-dated equity options using two Fourier-based pricing methods:  
   - Lewis (2001)  
   - Carr-Madan (1999)  
2. **Extension to the Bates (1996) jump-diffusion model** for longer-dated options, again calibrated via both Lewis and Carr-Madan frameworks.  
3. **Calibration and simulation of the Cox-Ingersoll-Ross (CIR, 1985) model** to the Euribor term structure for interest rate forecasting.

The notebook validates model consistency, compares pricing performance, and applies calibrated models to price exotic and vanilla options for client use cases.

## Project Structure

- **Input Data**:  
  - Option market prices (calls and puts) for two maturities: 15-day and 60-day.
  - Euribor zero-coupon rates for tenors: 1 week, 1 month, 3 months, 6 months, and 12 months.

- **Key Models Implemented**:
  - **Heston (1993)**: Stochastic volatility without jumps.
  - **Bates (1996)**: Heston + Merton-style log-normal jumps.
  - **CIR (1985)**: Mean-reverting short-rate model for interest rates.

- **Fourier Pricing Methods**:
  - **Lewis (2001)**: Uses integration over log-strike space via the characteristic function of log-price.
  - **Carr-Madan (1999)**: Damped Fourier inversion with direct integration (non-FFT version for stability).

- **Client Applications**:
  - Pricing an **ATM Asian call option** (20-day maturity) via Monte Carlo simulation under the calibrated Heston model.
  - Pricing a **70-day OTM put option** (95% moneyness) using the Bates–Carr-Madan framework.


## Technical Highlights

- **Model Calibration**:
  - All models are calibrated by minimizing **mean squared pricing error (MSE)** between model and market prices.
  - Puts are included via **put-call parity** for robustness and efficiency.
  - Parameter bounds and economic constraints (e.g., **Feller condition**) are enforced during optimization.
  - Optimizer: **L-BFGS-B** with tight convergence tolerance (`ftol=1e-10`).

- **Numerical Stability**:
  - Fallbacks in characteristic function evaluation to avoid division-by-zero or overflow.
  - Full-truncation Euler scheme for Monte Carlo simulation of Heston paths.
  - Cubic spline interpolation of the Euribor curve for smooth calibration target.

- **Validation**:
  - Visual diagnostics via scatter plots of market vs. model prices.
  - Comparison of calibrated parameters and MSE across pricing methods.
  - Confidence intervals and distributional analysis for interest rate simulations.


## Key Results

### Heston Calibration (15-day options)
- **MSE**: ~0.279 for both Lewis and Carr-Madan methods.
- **Parameters**:
  - κ ≈ 2.0 → fast mean reversion
  - ρ ≈ −0.86 → strong leverage effect
  - σ ≈ 0.59 → high vol-of-vol, captures smile
  - v₀ ≈ 0.096 → matches ATM implied vol (~31%)

### Bates Calibration (60-day options)
- **MSE**: ~1.35–1.38
- Two distinct parameter regimes emerge:
  - **Lewis**: High jump intensity (λ ≈ 5), small jumps (μⱼ ≈ −0.03)
  - **Carr-Madan**: Fewer but larger jumps (μⱼ = −0.30), stronger leverage (ρ ≈ −0.99)
- Highlights identifiability challenges in jump-diffusion models.

### CIR Calibration (Euribor curve)
- **MSE**: 6.8e⁻⁷ → near-perfect fit
- **Parameters**:
  - κ = 0.5064 → moderate mean reversion
  - θ = 0.1000 (upper bound) → reflects upward-sloping curve
  - σ = 0.0993 → moderate rate volatility
- **1-year forward 12-month Euribor forecast**:  
  - Expected: **3.41%** (vs. current 2.56%)  
  - 95% CI: **[1.67%, 5.74%]**

### Client Pricing
- **20-day ATM Asian Call**: **$4.85** (with 4% fee)
- **70-day 95% Put**: **$7.46** (with 4% fee)


## 📝 Notes

- The project assumes 250 trading days/year for equity options and 365 calendar days/year for interest rates.
- All pricing is under the **risk-neutral measure**.
- The 4% client fee covers execution, hedging, and risk management costs.

___

**Author**: Joseph Edet

**Date**: November 2025  

**License**: For educational and research purposes only. Not for production trading without rigorous validation.
