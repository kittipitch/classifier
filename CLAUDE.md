# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Classifier is a Python CLI tool that organizes files in a directory into categorized folders (Music, Documents, Pictures, Archives, etc.) based on file extensions or creation date. Cross-platform (macOS/Linux/Windows).

## Commands

```bash
# Install for development
pip install -e .

# Run
classifier                          # classify current directory
classifier -d /path -o /dest       # classify specific directory to destination
classifier -dt                      # classify by creation date

# Tests
python3 -m unittest discover -s tests -p "test_*.py"
python3 -m unittest tests.test_classifier.ClassifierTest          # single test class
python3 -m unittest tests.test_classifier.ClassifierTest.test_moveto  # single test
```

## Architecture

Single-module application centered on the `Classifier` class in `classifier/classifier.py`.

- **Entry point**: `classifier.classifier:main` (registered as console script in setup.py)
- **`Classifier.__init__()`** parses args via argparse and calls `self.run()`
- **`run()`** dispatches to the appropriate action: `classify()` (by extension), `classify_by_date()`, config management, etc.
- **Config system**: Platform-specific master config (`~/.classifier-master.conf`) plus optional per-directory `.classifier.conf`. Config maps category names to file extensions.
- **`arrow`** is the sole external dependency (used for date-based classification)

## Platform-Specific Behavior

Config editing (`-et`) uses platform-specific commands: `open -t` (macOS), `xdg-open` (Linux), `os.startfile` (Windows). Config file location also varies by platform.

## Testing

Tests in `tests/test_classifier.py` use `unittest`. setUp/tearDown create and clean up a `.unittest` temp directory with test files. Tests cover `moveto()` and date-based classification.
