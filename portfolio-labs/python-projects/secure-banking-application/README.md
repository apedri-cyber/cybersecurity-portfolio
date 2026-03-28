# Secure Banking Application Project

## Overview
Simple command-line banking application designed to allow users to create accounts, deposit and withdraw money, check balance, and transfer money between accounts. The focus of this project is data security, input validation, and authentication. 

## Key Features
### User Account Management
* Create an account with a unique username and password
* Secure login functionality by hashing passwords and implementing an account lockout mechanism

### Basic Banking Operations
* Deposit
* Withdraw
* Check Balance
* Transfer Funds

### Data Security
* Securely store passwords using the SHA-256 hash function

### Data Storage
* Simple file-based storage (JSON)
* Used to store user account information and transaction history

### Transaction Logging
* All transactions are logged with timestamps to maintain a record

### User Navigation
* Users can exit the menu or go back to any state
* Improves usability of the application

## Imported Libraries
* hashlib: Provides the interface needed to hash passwords using SHA-256
* json: Used for handling JSON data, which stores user account information and transaction history
* os: Allows interaction with the operating system, particularly checking for the existence of files
* getpass: Provides a secure and portable way to collect passwords from users without echoing them on screen
* time: Provides functions to handle time-related tasks, such as timestamps for transactions and account lockouts
* re: Used for validating the format of usernames and passwords

## Lessons Learned
* Implemented password hashing for the first time using Python's hashlib library, bridging the gap between knowing the concept and actually applying it in code
* Built an account lockout mechanism from scratch using timestamps to block users after repeated failed login attempts — translating a real-world security pattern into working logic
* Learned how to persist and retrieve application data using JSON files, replacing the need for a database while keeping data intact between sessions
* Practiced input validation across account creation and banking functions to handle edge cases and prevent invalid data from entering the system
* Debugged a data persistence issue where user accounts weren't surviving between sessions in Google Colab. Used print statements to trace the problem and identified that save_data() wasn't being called at the right points in the code. Fixed it by adding save_data() calls inside create_account(), authenticate(), deposit(), withdraw(), and transfer() to ensure all changes were written to the JSON file.
