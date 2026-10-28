# FX Hedging

This capability evaluates foreign-currency transaction exposure and compares the economics of forward, money-market, and option hedging strategies.

## Current Engagement

The capability is being exercised for a U.S. payroll-software firm's EUR 4,300,000 receivable from a Spanish bank, due in 365 days.

The firm's risk is a decline in EURUSD before settlement, which would reduce the U.S.-dollar value of the fixed euro receivable.

## Model Scope

The model compares:

- an unhedged EUR receivable;
- a 365-day EURUSD forward hedge;
- a three-step money-market hedge using EUR borrowing and USD investment;
- an EUR put used to establish a minimum level of USD proceeds while retaining upside participation; and
- an EUR call as a comparison case for the reversed exposure of an EUR payable.

The model uses simple annual interest with the course ACT/360 convention and includes a covered-interest-parity validation check and an eleven-scenario EURUSD sensitivity analysis from 95% to 105% of inception spot.

## Files

- `spec.md` — Stage 2 model specification defining inputs, workbook architecture, calculation logic, assumptions, sensitivity analysis, outputs, and validation rules.
- `model.xlsx` — Stage 3 Excel implementation of the specification. This file does not exist until the specification is complete and committed.

## Current Status

Stage 2 specification drafted, independently reviewed with AI, revised, and committed. Market-dependent placeholder inputs will be replaced with sourced and timestamped observations during Stage 4.
