```c
#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
#include <fstream>
#include <cmath>

using namespace std;

struct stInfoClient
{
	string AccountNumber;
	string PinCode;
	string Name;
	string PhoneNumber;
	double AccountBalance;
	bool MarkForDelete = false;
};


const string ClientFileName = "Client.txt";

stInfoClient CurrentClient;

enum enAtmMainMenueOption { eQuickWithdraw = 1, eNormalWithdraw = 2, eDeposit = 3, eCheckBalance = 4, eLogout = 5 };

void ShowATM_MainMenueScreen();
void GoBackToAtmMainMenue();
void Login();

vector <string> SplitString(string S1, string delim)
{
	int pos = 0;
	string sWord;

	vector <string>  vStirng;

	while ((pos = S1.find(delim)) != std::string::npos)
	{
		sWord = S1.substr(0, pos);
		if (sWord != "")
		{
			vStirng.push_back(sWord);
		}
		S1.erase(0, pos + delim.length());
	}

	if (S1 != "")
	{
		vStirng.push_back(S1);
	}

	return vStirng;

}
stInfoClient ConvertLineToRecord(string Line, string Seperator = "#//#")
{
	vector<string> vString = SplitString(Line, Seperator);

	stInfoClient Client;

	Client.AccountNumber = vString.at(0);
	Client.PinCode = vString.at(1);
	Client.Name = vString.at(2);
	Client.PhoneNumber = vString.at(3);
	Client.AccountBalance = stod(vString.at(4));

	return Client;

}
string ConvertRecordToLine(stInfoClient Client, string Seperator = "#//#")
{
	string stClientRecord = "";

	stClientRecord += Client.AccountNumber + Seperator;
	stClientRecord += Client.PinCode + Seperator;
	stClientRecord += Client.Name + Seperator;
	stClientRecord += Client.PhoneNumber + Seperator;
	stClientRecord += to_string(Client.AccountBalance);

	return stClientRecord;
}
void PrintClientCard(stInfoClient Client)
{
	cout << "\nThe following are the client details:\n";

	cout << "\n--------------------------------------------------------\n";
	cout << "Account Number   :" << Client.AccountNumber << endl;
	cout << "Pin Code         :" << Client.PinCode << endl;
	cout << "Name             :" << Client.Name << endl;
	cout << "Phone Number     :" << Client.PhoneNumber << endl;
	cout << "Account Balance  :" << Client.AccountBalance << endl;
	cout << "--------------------------------------------------------\n";

}
string ReadClientAccountNumber()
{
	string AccountNumber;

	cout << "Please Enter The Account Number? ";
	cin >> AccountNumber;

	return AccountNumber;
}
string ReadClientPin()
{
	string Pin = "";

	cout << "Enter Pin?";
	cin >> Pin;

	return Pin;
}
bool ClientExistsByAccountNumber(string AccountNumber, string FileName)
{
	vector<stInfoClient> vClient;
	fstream MyFlile;

	MyFlile.open(FileName, ios::in);

	if (MyFlile.is_open())
	{
		string Line;
		stInfoClient Client;

		while (getline(MyFlile, Line))
		{
			Client = ConvertLineToRecord(Line);

			if (Client.AccountNumber == AccountNumber)
			{
				MyFlile.close();
				return true;
			}
			vClient.push_back(Client);
		}
		MyFlile.close();
	}
	return false;
}
vector<stInfoClient> LoadClientDataFromFile(string FileName)
{
	vector<stInfoClient> vClient;

	fstream MyFile;
	MyFile.open(FileName, ios::in);

	if (MyFile.is_open())
	{
		string Line;
		stInfoClient Client;

		while (getline(MyFile, Line))
		{
			Client = ConvertLineToRecord(Line);

			vClient.push_back(Client);
		}

		MyFile.close();
	}

	return vClient;
}
void PrintClientRecord(stInfoClient Client)
{
	cout << "| " << setw(15) << left << Client.AccountNumber;
	cout << "| " << setw(10) << left << Client.PinCode;
	cout << "| " << setw(40) << left << Client.Name;
	cout << "| " << setw(12) << left << Client.PhoneNumber;
	cout << "| " << setw(12) << left << Client.AccountBalance;
}
void AddDataLineToFile(string FileName, string stDataLine)
{
	fstream MyFile;
	MyFile.open(FileName, ios::out | ios::app);

	if (MyFile.is_open())
	{
		MyFile << stDataLine << endl;

		MyFile.close();
	}
}
vector<stInfoClient> SaveClientDataToFile(string FileName, vector<stInfoClient> vClient)
{
	fstream MyFile;
	MyFile.open(ClientFileName, ios::out);

	string DataLine;
	if (MyFile.is_open())
	{
		for (stInfoClient C : vClient)
		{
			if (C.MarkForDelete == false)
			{
				DataLine = ConvertRecordToLine(C);
				MyFile << DataLine << endl;
			}
		}
		MyFile.close();
	}
	return vClient;
}
bool DepositBalanceToClientByAccountNumber(string AccountNumber, double Amount, vector <stInfoClient>& vClients)
{


	char Answer = 'n';


	cout << "\n\nAre you sure you want perfrom this transaction? y/n ? ";
	cin >> Answer;
	if (Answer == 'y' || Answer == 'Y')
	{

		for (stInfoClient& C : vClients)
		{
			if (C.AccountNumber == CurrentClient.AccountNumber)
			{
				C.AccountBalance += Amount;
				CurrentClient.AccountBalance = C.AccountBalance;
				SaveClientDataToFile(ClientFileName, vClients);
				cout << "\n\nDone Successfully. New balance is: " << C.AccountBalance;

				return true;
			}

		}


		return false;
	}

}

//Quick Withdraw
void PrintQuickWithdrawList()
{
	cout << "\n---------------------------------------------------------\n";
	cout << "       [1] 20          [2] 50                            \n";
	cout << "       [3] 100         [4] 200                           \n";
	cout << "       [5] 400         [6] 600                           \n";
	cout << "       [7] 800         [8] 1000                          \n";
	cout << "       [9] Exit                                          \n";
	cout << "---------------------------------------------------------\n";
}
short ReadQuickWithdrawOption()
{
	short Choice = 0;
	while (Choice < 1 || Choice>9)
	{
		cout << "\nChoose what to do from [1] to [9] ? ";
		cin >> Choice;
	}
	return Choice;
}
short getQuickWithDrawAmount(short QuickWithDrawOption)
{
	switch (QuickWithDrawOption)
	{
	case 1:
		return 20;
	case 2:
		return 50;
	case 3:
		return 100;
	case 4:
		return 200;
	case 5:
		return 400;
	case 6:
		return 600;
	case 7:
		return 800;
	case 8:
		return 1000;
	default:
		return 0;
	}
}
void ShowQiuickWithdrawScreen()
{
	cout << "\n---------------------------------------------------------------------\n";
	cout << "\t\tQuick Withdraw Screen";
	cout << "\n---------------------------------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);


	PrintQuickWithdrawList();
	cout << "Your Balance is:" << CurrentClient.AccountBalance << endl;
	
	short Amount = getQuickWithDrawAmount(ReadQuickWithdrawOption());
	
	while (Amount > CurrentClient.AccountBalance)
	{
		cout << "\nAmount Exceeds the balance, you can withdraw up to : " << CurrentClient.AccountBalance << endl;
		Amount = getQuickWithDrawAmount(ReadQuickWithdrawOption());
	}

	DepositBalanceToClientByAccountNumber(CurrentClient.AccountNumber, Amount * (-1), vClient);
}

//Normal Withdraw
bool IsMultiple5(int Amount)
{
	return Amount % 5 == 0;
}
void ShowNormalWithdrawScreen()
{
	cout << "\n---------------------------------------------------------------------\n";
	cout << "\t\tNormal Withdraw Screen";
	cout << "\n---------------------------------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = CurrentClient.AccountNumber;

	double Amount = 0;
	do
	{
		cout << "\nEnter an amount multiple of 5's? ";
		cin >> Amount;
	} while (!IsMultiple5(Amount));


	while (Amount > CurrentClient.AccountBalance)
	{
		cout << "\nAmount Exceeds the balance, you can withdraw up to : " << CurrentClient.AccountBalance << endl;
		cout << "Enter an amount multiple of 5's? ";
		cin >> Amount;
	}

	DepositBalanceToClientByAccountNumber(AccountNumber, Amount * (-1), vClient);
}

//Deposit : 
double ReadDepositAmount()
{
	int Amount = 0;
	cout << "\nEnter Positive Deposit Amount? ";
	cin >> Amount;

	while (Amount <= 0){
		cout << "\nEnter Positive Deposit Amount? ";
		cin >> Amount;
	}

	return Amount;
	
}
void ShowDepositScreen()
{
	cout << "\n---------------------------------------------------------------------\n";
	cout << "\t\tDeposit Screen";
	cout << "\n---------------------------------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = CurrentClient.AccountNumber;

	double Amount = ReadDepositAmount();

	DepositBalanceToClientByAccountNumber(AccountNumber, Amount, vClient);
}

//Check Balance
void ShowCheckBalanceByAccountNumber()
{
	cout << "\n---------------------------------------------------------------------\n";
	cout << "\t\tCheck Balance Screen";
	cout << "\n---------------------------------------------------------------------\n";

	cout << "Your Balance is: " << CurrentClient.AccountBalance << endl;

}

void GoBackToAtmMainMenue()
{
	cout << "\n\nPress any key to go back to atm main menue...";
	system("pause > 0");
	ShowATM_MainMenueScreen();
}

bool FindClientByAccountNumberAndPin(string AccountNumber, string Pin, stInfoClient& Client)
{
	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);

	for (stInfoClient C : vClient)
	{
		if (C.AccountNumber == AccountNumber && C.PinCode == Pin)
		{
			Client = C;
			return true;
		}
	}
	return false;
}

short ReadATM_MainMenueOption()
{
	short Choice;

	cout << "Choice what do you want to do? [1 to 5]? ";
	cin >> Choice;

	return Choice;
}
void PerformATM_MainMenueOption(enAtmMainMenueOption AtmMainMenueOption)
{
	switch (AtmMainMenueOption)
	{
	case enAtmMainMenueOption::eQuickWithdraw:
	{
		system("cls");
		ShowQiuickWithdrawScreen();
		GoBackToAtmMainMenue();
		break;
	}
	case enAtmMainMenueOption::eNormalWithdraw:
	{
		system("cls");
		ShowNormalWithdrawScreen();
		GoBackToAtmMainMenue();
		break;
	}
	case enAtmMainMenueOption::eDeposit:
	{
		system("cls");
		ShowDepositScreen();
		GoBackToAtmMainMenue();
		break;

	}
	case enAtmMainMenueOption::eCheckBalance:
	{
		system("cls");
		ShowCheckBalanceByAccountNumber();
		GoBackToAtmMainMenue();
		break;
	}
	case enAtmMainMenueOption::eLogout:
	{
		system("cls");
		Login();
		break;
	}
	}
}
void ShowATM_MainMenueScreen()
{
	system("cls");

	cout << "================================================================================================\n\n";
	cout << "                                ATM Main Menue Screan                                       \n\n";
	cout << "================================================================================================\n";
	cout << "       [ 1 ] Quick Withdraw" << endl;
	cout << "       [ 2 ] Normal Withdraw" << endl;
	cout << "       [ 3 ] Deposit" << endl;
	cout << "       [ 4 ] Check Balance" << endl;
	cout << "       [ 5 ] Log out" << endl;
	cout << "================================================================================================\n\n";
	PerformATM_MainMenueOption((enAtmMainMenueOption)ReadATM_MainMenueOption());
}

//Login
bool LoadAccountNumberInfo(string AccountNumber, string Pin)
{
	if (FindClientByAccountNumberAndPin(AccountNumber, Pin, CurrentClient))
		return true;
	else
		return false;
}
void Login()
{
	bool LoginFaild = false;
	string AccountNumber, Pin;

	do
	{
		system("cls");

		cout << "\n------------------------------------------\n";
		cout << "\t\tLogin Screen";
		cout << "\n------------------------------------------\n";

		if (LoginFaild)
		{
			cout << "\nInvalid AccountNumber/Pin\n" << endl;
		}

		string AccountNumber = ReadClientAccountNumber();
		string Pin = ReadClientPin();

		LoginFaild = !LoadAccountNumberInfo(AccountNumber, Pin);

	} while (LoginFaild);

	ShowATM_MainMenueScreen();
}

int main()
{
	Login();

	return 0;
}

```
