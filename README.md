CodeAlpha_Banking_System
# banking_system.cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Transaction history ke liye structure
struct Transaction {
    string type;
    float amount;
    string toFrom;
};

// Account class
class Account {
public:
    int accountNumber;
    string customerName;
    float balance;
    vector<Transaction> transactions;
    
    Account() {
        accountNumber = 0;
        customerName = "";
        balance = 0;
    }
    
    Account(int accNo, string name) {
        accountNumber = accNo;
        customerName = name;
        balance = 0;
    }
    
    // Deposit money
    void deposit(float amount) {
        balance += amount;
        Transaction t;
        t.type = "DEPOSIT";
        t.amount = amount;
        t.toFrom = "-";
        transactions.push_back(t);
        cout << "\n✅ Deposited: Rs. " << amount << endl;
        cout << "New Balance: Rs. " << balance << endl;
    }
    
    // Withdraw money
    bool withdraw(float amount) {
        if(amount > balance) {
            cout << "\n❌ Insufficient balance! Current balance: Rs. " << balance << endl;
            return false;
        }
        balance -= amount;
        Transaction t;
        t.type = "WITHDRAW";
        t.amount = amount;
        t.toFrom = "-";
        transactions.push_back(t);
        cout << "\n✅ Withdrawn: Rs. " << amount << endl;
        cout << "New Balance: Rs. " << balance << endl;
        return true;
    }
    
    // Transfer money
    bool transfer(float amount, Account &receiver) {
        if(amount > balance) {
            cout << "\n❌ Insufficient balance! Current balance: Rs. " << balance << endl;
            return false;
        }
        balance -= amount;
        receiver.balance += amount;
        
        Transaction t1, t2;
        t1.type = "TRANSFER SENT";
        t1.amount = amount;
        t1.toFrom = receiver.customerName + " (Acc: " + to_string(receiver.accountNumber) + ")";
        transactions.push_back(t1);
        
        t2.type = "TRANSFER RECEIVED";
        t2.amount = amount;
        t2.toFrom = customerName + " (Acc: " + to_string(accountNumber) + ")";
        receiver.transactions.push_back(t2);
        
        cout << "\n✅ Transferred Rs. " << amount << " to " << receiver.customerName << endl;
        cout << "Your New Balance: Rs. " << balance << endl;
        return true;
    }
    
    // Show transaction history
    void showTransactions() {
        cout << "\n========== TRANSACTION HISTORY ==========\n";
        if(transactions.empty()) {
            cout << "No transactions yet.\n";
            return;
        }
        for(int i = 0; i < transactions.size(); i++) {
            cout << i+1 << ". " << transactions[i].type << " | Amount: Rs. " << transactions[i].amount;
            if(transactions[i].toFrom != "-") {
                cout << " | " << transactions[i].toFrom;
            }
            cout << endl;
        }
        cout << "=========================================\n";
    }
    
    void displayInfo() {
        cout << "\n========== ACCOUNT INFO ==========\n";
        cout << "Customer Name: " << customerName << endl;
        cout << "Account Number: " << accountNumber << endl;
        cout << "Balance: Rs. " << balance << endl;
        cout << "===================================\n";
    }
};

// Banking System class
class BankingSystem {
private:
    vector<Account> accounts;
    int nextAccountNumber;
    
    Account* findAccount(int accNo) {
        for(int i = 0; i < accounts.size(); i++) {
            if(accounts[i].accountNumber == accNo) {
                return &accounts[i];
            }
        }
        return NULL;
    }
    
public:
    BankingSystem() {
        nextAccountNumber = 1001;
    }
    
    void createAccount() {
        string name;
        cout << "\nEnter customer name: ";
        cin.ignore();
        getline(cin, name);
        
        Account newAccount(nextAccountNumber, name);
        accounts.push_back(newAccount);
        
        cout << "\n✅ Account created successfully!" << endl;
        cout << "Customer Name: " << name << endl;
        cout << "Account Number: " << nextAccountNumber << endl;
        cout << "Initial Balance: Rs. 0" << endl;
        
        nextAccountNumber++;
    }
    
    void depositMoney() {
        int accNo;
        float amount;
        
        cout << "\nEnter account number: ";
        cin >> accNo;
        
        Account* acc = findAccount(accNo);
        if(acc == NULL) {
            cout << "\n❌ Account not found!" << endl;
            return;
        }
        
        cout << "Enter amount to deposit: Rs. ";
        cin >> amount;
        
        if(amount <= 0) {
            cout << "\n❌ Invalid amount!" << endl;
            return;
        }
        
        acc->deposit(amount);
    }
    
    void withdrawMoney() {
        int accNo;
        float amount;
        
        cout << "\nEnter account number: ";
        cin >> accNo;
        
        Account* acc = findAccount(accNo);
        if(acc == NULL) {
            cout << "\n❌ Account not found!" << endl;
            return;
        }
        
        cout << "Enter amount to withdraw: Rs. ";
        cin >> amount;
        
        if(amount <= 0) {
            cout << "\n❌ Invalid amount!" << endl;
            return;
        }
        
        acc->withdraw(amount);
    }
    
    void transferMoney() {
        int fromAcc, toAcc;
        float amount;
        
        cout << "\nEnter your account number: ";
        cin >> fromAcc;
        
        Account* sender = findAccount(fromAcc);
        if(sender == NULL) {
            cout << "\n❌ Sender account not found!" << endl;
            return;
        }
        
        cout << "Enter receiver account number: ";
        cin >> toAcc;
        
        Account* receiver = findAccount(toAcc);
        if(receiver == NULL) {
            cout << "\n❌ Receiver account not found!" << endl;
            return;
        }
        
        cout << "Enter amount to transfer: Rs. ";
        cin >> amount;
        
        if(amount <= 0) {
            cout << "\n❌ Invalid amount!" << endl;
            return;
        }
        
        sender->transfer(amount, *receiver);
    }
    
    void showAccountInfo() {
        int accNo;
        
        cout << "\nEnter account number: ";
        cin >> accNo;
        
        Account* acc = findAccount(accNo);
        if(acc == NULL) {
            cout << "\n❌ Account not found!" << endl;
            return;
        }
        
        acc->displayInfo();
    }
    
    void showTransactionHistory() {
        int accNo;
        
        cout << "\nEnter account number: ";
        cin >> accNo;
        
        Account* acc = findAccount(accNo);
        if(acc == NULL) {
            cout << "\n❌ Account not found!" << endl;
            return;
        }
        
        acc->showTransactions();
    }
    
    void showAllAccounts() {
        if(accounts.empty()) {
            cout << "\n❌ No accounts in the system!" << endl;
            return;
        }
        
        cout << "\n========== ALL ACCOUNTS ==========\n";
        for(int i = 0; i < accounts.size(); i++) {
            cout << accounts[i].customerName << " | Acc No: " << accounts[i].accountNumber << " | Balance: Rs. " << accounts[i].balance << endl;
        }
        cout << "===================================\n";
    }
};

// Main function
int main() {
    BankingSystem bank;
    int choice;
    
    cout << "\n========================================\n";
    cout << "       WELCOME TO BANKING SYSTEM        \n";
    cout << "========================================\n";
    
    do {
        cout << "\n========== MAIN MENU ==========\n";
        cout << "1. Create New Account\n";
        cout << "2. Deposit Money\n";
        cout << "3. Withdraw Money\n";
        cout << "4. Transfer Money\n";
        cout << "5. Check Account Info\n";
        cout << "6. View Transaction History\n";
        cout << "7. View All Accounts\n";
        cout << "8. Exit\n";
        cout << "===============================\n";
        cout << "Enter your choice: ";
        cin >> choice;
        
        switch(choice) {
            case 1:
                bank.createAccount();
                break;
            case 2:
                bank.depositMoney();
                break;
            case 3:
                bank.withdrawMoney();
                break;
            case 4:
                bank.transferMoney();
                break;
            case 5:
                bank.showAccountInfo();
                break;
            case 6:
                bank.showTransactionHistory();
                break;
            case 7:
                bank.showAllAccounts();
                break;
            case 8:
                cout << "\n✅ Thank you for using Banking System!\n";
                cout << "   Have a great day!\n";
                break;
            default:
                cout << "\n❌ Invalid choice! Try again.\n";
        }
    } while(choice != 8);
    
    return 0;
}