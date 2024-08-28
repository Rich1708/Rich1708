#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAX_USERS 10
#define PIN_LENGTH 4
#define NAME_LENGTH 50

typedef struct
{
    char name[NAME_LENGTH];
    char pin[PIN_LENGTH + 1];
    float balance;
} User;

User users[MAX_USERS];
int userCount = 0;
int currentUserIndex = -1;


void generateRandomPIN(char* pin);
int findUserIndexByName(const char* name);
int authenticateUser(const char* name, const char* pin);
void displayMenu();
void checkBalance();
void depositMoney();
void withdrawMoney();
void logOut();

int main() 
{
    char name[NAME_LENGTH];
    char pin[PIN_LENGTH + 1];
    int choice;

   
    srand(time(NULL));

    printf("Welcome to the ATM Machine!\n");

    while (1)
      {
        printf("Please enter your name (or 'exit' to exit): ");
        scanf("%s", name);

        if (strcmp(name, "exit") == 0)
        {
            printf("Exiting the ATM. Goodbye!\n");
            break;
        }

        int userIndex = findUserIndexByName(name);
        if (userIndex == -1) 
        {
            generateRandomPIN(pin);
            printf("%s, your generated ATM pin is : %s\n", name, pin);
            strcpy(users[userCount].name, name);
            strcpy(users[userCount].pin, pin);
            users[userCount].balance = 1000.0; 
            userCount++;
            userIndex = userCount - 1;
        } 
        else 
        {
            strcpy(pin, users[userIndex].pin);
        }

        printf("Please enter your 4-digit PIN: ");
        scanf("%s", pin);

        if (authenticateUser(name, pin)) 
        {
            printf("Login successful!\n");

            while (1) {
                displayMenu();
                printf("Enter your choice: ");
                scanf("%d", &choice);

                switch (choice) 
               {
                    case 1:
                        checkBalance();
                        break;
                    case 2:
                        depositMoney();
                        break;
                    case 3:
                        withdrawMoney();
                        break;
                    case 4:
                        logOut();
                        break;
                    default:
                        printf("Invalid choice! Please try again.\n");
                        break;
                }

                if (currentUserIndex == -1)         
                {
                    break;
                }
            }
        } 
        else 
        {
            printf("Login failed! Please check your name or PIN.\n");
        }
    }

    return 0;
}

void generateRandomPIN(char* pin) 
{
    snprintf(pin, PIN_LENGTH + 1, "%04d", rand() % 10000);
}

int findUserIndexByName(const char* name) 
{
    for (int i = 0; i < userCount; i++) 
    {
        if (strcmp(users[i].name, name) == 0) 
        {
            return i;
        }
    }
    return -1;
}

int authenticateUser(const char* name, const char* pin)
{
    int index = findUserIndexByName(name);
    if (index != -1 && strcmp(users[index].pin, pin) == 0) 
    {
        currentUserIndex = index;
        return 1;
    }
    return 0;
}

void displayMenu() 
{
    printf("\nATM Menu:\n");
    printf("1. Check Balance\n");
    printf("2. Deposit\n");
    printf("3. Withdraw\n");
    printf("4. Logout\n");
}

void checkBalance() 
{
    if (currentUserIndex != -1) 
    {
        printf("Account Balance for %s: ₹%.2f\n", users[currentUserIndex].name, users[currentUserIndex].balance);
    }
}

void depositMoney() 
{
    float amount;
    if (currentUserIndex != -1) 
    {
        printf("Enter deposit amount for %s: ₹", users[currentUserIndex].name);
        scanf("%f", &amount);
        if (amount > 0) 
        {
            users[currentUserIndex].balance += amount;
            printf("Deposit successful. New balance for %s: ₹%.2f\n", users[currentUserIndex].name, users[currentUserIndex].balance);
        } 
     else 
        {
            printf("Invalid amount!\n");
        }
    }
}

void withdrawMoney() 
{
    float amount;
    if (currentUserIndex != -1) 
      {
        printf("Enter withdrawal amount for %s: ₹", users[currentUserIndex].name);
        scanf("%f", &amount);
        if (amount > 0 && amount <= users[currentUserIndex].balance) 
        {
            users[currentUserIndex].balance -= amount;
            printf("Withdrawal successful. New balance for %s: ₹%.2f\n", users[currentUserIndex].name, users[currentUserIndex].balance);
        }
        else 
        {
            printf("Insufficient funds or invalid amount!\n");
        }
    }
}

void logOut() 
{
    currentUserIndex = -1;
    printf("Logging out...\n");
}
