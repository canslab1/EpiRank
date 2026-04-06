# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## v2.0.0 (2026-03-07)

Complete rewrite from Python 2.7 to Python 3.

### Added
- PySide6 graphical user interface with 14 interactive tabs
- Reproduces all key figures and tables from the paper
- Head/tail breaks classification for township risk levels
- Sensitivity analysis for daytime parameter
- Comparison with PageRank and HITS centrality measures
- Modern dependency management (requirements.txt)
- Comprehensive README documentation

### Changed
- Migrated from Python 2.7 to Python 3.10+
- Replaced legacy GUI framework with PySide6
- Updated data I/O to use openpyxl for Excel files

## v1.0.0 (2014)

Original implementation by Chung-Yuan Huang.

- Core EpiRank algorithm with bidirectional commuting model
- Asymmetric OD matrix processing
- Disease correlation analysis (Influenza, Enterovirus, SARS)
- Matplotlib visualization
