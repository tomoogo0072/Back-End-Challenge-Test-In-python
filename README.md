# Back-End-Challenge-Test-In-python
For Cumputer science Lab entrance Test
import random

MAX_PLAYERS = 100

class Player:
    def __init__(self, id, name, phone, password, ip, originUrl):
        self.id = id
        self.name = name
        self.phone = phone
        self.password = password
        self.ip = ip
        self.originUrl = originUrl
        self.balance = 0
        self.numReferrals = 0
        self.referralIds = []

players = []
numPlayers = 0

def registerPlayer():
    global numPlayers
    
    if numPlayers >= MAX_PLAYERS:
        print("Maximum number of players reached.")
        return
    
    name = input("Enter player name: ")
    phone = input("Enter phone number: ")
    password = input("Enter password: ")
    ip = input("Enter IP address: ")
    originUrl = input("Enter origin URL: ")
    
    alphabets = "abcdefghijklmnopqrstuvwxyz"
    randId = ''.join(random.choice(alphabets) for _ in range(5)) + ''.join(random.choice('0123456789') for _ in range(5))
    
    player = Player(randId, name, phone, password, ip, originUrl)
    players.append(player)
    numPlayers += 1
    
    print("\nPlayer successfully registered.")
    print("Player id:", randId)

def login():
    playerIdx = -1
    
    playerId = input("Enter player id: ")
    password = input("Enter password: ")
    
    for i, player in enumerate(players):
        if player.id == playerId:
           playerIdx = i
           break

    if playerIdx != -1:
        if players[playerIdx].password == password:
            print("\nPlayer successfully logged in.")
            print("Player name:", players[playerIdx].name)
            print("Player balance:", players[playerIdx].balance)
        else:
            print("Invalid password.")
    else:
        print("Invalid player id.")

def deposit():
    playerIdx = -1
    
    playerId = input("Enter player id: ")
    amount = float(input("Enter deposit amount: "))
    
    for i, player in enumerate(players):
        if player.id == playerId:
            playerIdx = i
            break
    
    if playerIdx != -1:
        players[playerIdx].balance += amount
        print("\nDeposit successful.")
        print("Player balance:", players[playerIdx].balance)
    else:
        print("Invalid player id.")

def withdraw():
    playerIdx = -1
    
    playerId = input("Enter player id: ")
    amount = float(input("Enter withdrawal amount: "))
    
    for i, player in enumerate(players):
        if player.id == playerId:
            playerIdx = i
            break
    
    if playerIdx != -1:
        if players[playerIdx].balance >= amount:
            players[playerIdx].balance -= amount
            print("\nWithdrawal successful.")
            print("Player balance:", players[playerIdx].balance)
        else:
            print("Insufficient balance.")
    else:
        print("Invalid player id.")

def generateReferralLink():
    playerIdx = -1
    
    playerId = input("Enter player id: ")
    
    for i, player in enumerate(players):
        if player.id == playerId:
            playerIdx = i
            break
    
    if playerIdx != -1:
        print("Referral link: https://example.com/register?ref=" + players[playerIdx].id)
    else:
        print("Invalid player id.")

def registerReferral():
    playerIdx = -1
    referralIdx = -1
    
    playerId = input("Enter player id: ")
    referralId = input("Enter referral id: ")
    
    for i, player in enumerate(players):
        if player.id == playerId:
            playerIdx = i
            break
    
    if playerIdx != -1:
        for i, player in enumerate(players):
            if player.id == referralId:
                referralIdx = i
                break
        
        if referralIdx != -1:
            players[playerIdx].referralIds.append(referralId)
            players[referralIdx].numReferrals += 1
            print("Referral successfully registered.")
        else:
            print("Invalid referral id.")
    else:
        print("Invalid player id.")

def main():
    while True:
        print("==============================")
        print("1. Register Player")
        print("2. Login")
        print("3. Deposit")
        print("4. Withdraw")
        print("5. Generate Referral Link")
        print("6. Register Referral")
        print("7. Exit")
        print("==============================")
        
        choice = input("Enter your choice: ")
        
        if choice == '1':
            registerPlayer()
        elif choice == '2':
            login()
        elif choice == '3':
            deposit()
        elif choice == '4':
            withdraw()
        elif choice == '5':
            generateReferralLink()
        elif choice == '6':
            registerReferral()
        elif choice == '7':
            print("Exiting program. Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
