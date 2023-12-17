
#include <iostream>
#include <string>
#include <vector>
#include <cstring>
#include <ctime>
#include <cstdlib>

#define MAX_PLAYERS 100

// Structure to store player information
struct Player {
    std::string id;
    std::string name;
    std::string phone;
    std::string password;
    std::string ip;
    std::string originUrl;
    float balance;
    int numReferrals;
    std::string referralIds[MAX_PLAYERS];
};

// Function prototypes
void registerPlayer();
void login();
void deposit();
void withdraw();
void generateReferralLink();
void registerReferral();
void creditRegistrationBonus(int parentIdx);
void creditDepositBonus(int parentIdx, float amount);
void getReferredPlayers();
void getTotalEarnings();
void calculateHash(const std::string& password, std::string& hashedPassword);
int validatePlayerId(const std::string& playerId);

// Global variables
Player players[MAX_PLAYERS];
int numPlayers = 0;

int main() {
    int choice;

    do {
        std::cout << "1. Register player" << std::endl;
        std::cout << "2. Login" << std::endl;
        std::cout << "3. Deposit" << std::endl;
        std::cout << "4. Withdraw" << std::endl;
        std::cout << "5. Generate referral link" << std::endl;
        std::cout << "6. Register referral" << std::endl;
        std::cout << "7. Get referred players" << std::endl;
        std::cout << "8. Get total earnings" << std::endl;
        std::cout << "9. Exit" << std::endl;
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        switch (choice) {
            case 1:
                registerPlayer();
                break;
            case 2:
                login();
                break;
            case 3:
                deposit();
                break;
            case 4:
                withdraw();
                break;
            case 5:
                generateReferralLink();
                break;
            case 6:
                registerReferral();
                break;
            case 7:
                getReferredPlayers();
                break;
            case 8:
                getTotalEarnings();
                break;
            case 9:
                std::cout << "Exiting..." << std::endl;
                break;
            default:
                std::cout << "Invalid choice. Please try again." << std::endl;
        }
    } while (choice != 9);

    return 0;
}

void registerPlayer() {
    if (numPlayers >= MAX_PLAYERS) {
        std::cout << "Maximum number of players reached." << std::endl;
        return;
    }

    Player player;

    // Get player details
    std::cout << "Enter player name: ";
    std::cin >> player.name;
    std::cout << "Enter phone number: ";
    std::cin >> player.phone;
    std::cout << "Enter password: ";
    std::cin >> player.password;
    std::cout << "Enter IP address: ";
    std::cin >> player.ip;
    std::cout << "Enter origin URL: ";
    std::cin >> player.originUrl;

    // Generate player id
    std::string alphabets = "abcdefghijklmnopqrstuvwxyz";
    int randIdx;
    for (int i = 0; i < 5; i++) {
        randIdx = rand() % 26;
        player.id.push_back(alphabets[randIdx]);
    }
    for (int i = 5; i < 10; i++) {
        player.id.push_back('0' + rand() % 10); // convert digit to character
    }

    // Set initial balance to 0
    player.balance = 0;
    player.numReferrals = 0;

    players[numPlayers++] = player;

    std::cout << "\nPlayer successfully registered." << std::endl;
    std::cout << "Player id: " << player.id << std::endl;
}

void login() {
    std::string playerId;
    std::string password;

    std::cout << "Enter player id: ";
    std::cin >> playerId;
    std::cout << "Enter password: ";
    std::cin >> password;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        // Player id is valid, check password
        std::string hashedPassword;
        calculateHash(password, hashedPassword);

        if (players[playerIdx].password == hashedPassword) {
            std::cout << "\nPlayer successfully logged in." << std::endl;
            std::cout << "Player name: " << players[playerIdx].name << std::endl;
            std::cout << "Player balance: " << players[playerIdx].balance << std::endl;
        } else {
            std::cout << "Invalid password." << std::endl;
        }
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void deposit() {
    std::string playerId;
    float amount;

    std::cout << "Enter player id: ";
    std::cin >> playerId;
    std::cout << "Enter deposit amount: ";
    std::cin >> amount;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        players[playerIdx].balance += amount;
        creditDepositBonus(playerIdx, amount);
        std::cout << "\nDeposit successful." << std::endl;
        std::cout << "Player balance: " << players[playerIdx].balance << std::endl;
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void withdraw() {
    std::string playerId;
    float amount;

    std::cout << "Enter player id: ";
    std::cin >> playerId;
    std::cout << "Enter withdrawal amount: ";
    std::cin >> amount;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        if (players[playerIdx].balance >= amount) {
            players[playerIdx].balance -= amount;
            std::cout << "\nWithdrawal successful." << std::endl;
            std::cout << "Player balance: " << players[playerIdx].balance << std::endl;
        } else {
            std::cout << "Insufficient balance." << std::endl;
        }
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void generateReferralLink() {
    std::string playerId;

    std::cout << "Enter player id: ";
    std::cin >> playerId;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        std::cout << "Referral link: https://example.com/register?ref=" << players[playerIdx].id << std::endl;
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void registerReferral() {
    std::string playerId;
    std::string referralId;

    std::cout << "Enter player id: ";
    std::cin >> playerId;
    std::cout << "Enter referral id: ";
    std::cin >> referralId;

    int playerIdx = validatePlayerId(playerId);
    int referralIdx = validatePlayerId(referralId);

    if (playerIdx != -1 && referralIdx != -1) {
        if (playerIdx == referralIdx) {
            std::cout << "Cannot refer yourself." << std::endl;
        } else {
            for (int i = 0; i < players[playerIdx].numReferrals; i++) {
                if (players[playerIdx].referralIds[i] == referralId) {
                    std::cout << "Player already referred." << std::endl;
                    return;
                }
            }

            players[playerIdx].referralIds[players[playerIdx].numReferrals++] = referralId;

            creditRegistrationBonus(referralIdx);
            std::cout << "Referral registration successful." << std::endl;
        }
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void creditRegistrationBonus(int parentIdx) {
    // Code to credit registration bonus to the parent player
    std::cout << "Registration bonus credited to parent." << std::endl;
}

void creditDepositBonus(int parentIdx, float amount) {
    // Code to credit deposit bonus to the parent player
    std::cout << "Deposit bonus credited to parent." << std::endl;
}

void getReferredPlayers() {
    std::string playerId;

    std::cout << "Enter player id: ";
    std::cin >> playerId;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        for (int i = 0; i < players[playerIdx].numReferrals; i++) {
            std::cout << players[playerIdx].referralIds[i] << std::endl;
        }
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void getTotalEarnings() {
    std::string playerId;

    std::cout << "Enter player id: ";
    std::cin >> playerId;

    int playerIdx = validatePlayerId(playerId);

    if (playerIdx != -1) {
        // Code to calculate total earnings for the player
        std::cout << "Total earnings: 0" << std::endl;
    } else {
        std::cout << "Invalid player id." << std::endl;
    }
}

void calculateHash(const std::string& password, std::string& hashedPassword) {
    // Code to calculate the hash value of password
    hashedPassword = password;
}

int validatePlayerId(const std::string& playerId) {
    for (int i = 0; i < numPlayers; i++) {
        if (players[i].id == playerId) {
            return i;
        }
    }
    return -1;
}
