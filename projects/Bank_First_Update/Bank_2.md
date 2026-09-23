```c
#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
#include <fstream>
#include <cmath>

using namespace std;

const string ClientFileName = "Client.txt";

void ShowMainMenue();
void ShowTransactionMenueScreen();

struct stInfoClient
{
	string AccountNumber;
	string PinCode;
	string Name;
	string PhoneNumber;
	double AccountBalance;
	bool MarkForDelete = false;
};
enum enMainMenueOption { eShowClientList = 1, eAddClientToSystem = 2, eDeleteClient = 3, eUpdateClientInfo = 4, eFindClient = 5, eTransaction = 6, eExit = 7 };
enum enTransactionMenueOption { eDeposit = 1, eWithdraw = 2, eTotalBalance = 3, eMainMenue = 4 };

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

//ShowClientList :
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
void ShowAllClientScreen()
{
	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);

	cout << "\n\t\t\t\t\t\tClient List (" << vClient.size() << ") Client(S).";
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";
	cout << "| " << left << setw(15) << "AccountNumber";
	cout << "| " << left << setw(10) << "PinCode";
	cout << "| " << left << setw(40) << "Name";
	cout << "| " << left << setw(12) << "PhoneNumber";
	cout << "| " << left << setw(12) << "AccountBalance";
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

	if (vClient.size() == 0)
	{
		cout << "\t\t\t\tNo Clients Available In the System!";
	}
	else
	{
		for (stInfoClient Client : vClient)
		{
			PrintClientRecord(Client);
			cout << endl;
		}
	}
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

}


//Add New Client :
stInfoClient ReadNewClient()
{
	stInfoClient Client;

	cout << "\nEnter Account Number? ";
	getline(cin >> ws, Client.AccountNumber);

	while (ClientExistsByAccountNumber(Client.AccountNumber, ClientFileName))
	{
		cout << "\nClient with [" << Client.AccountNumber << "] already exists, Enter another Account Number? ";
		getline(cin >> ws, Client.AccountNumber);
	}

	cout << "Enter Pin Code? ";
	getline(cin, Client.PinCode);

	cout << "Enter Name? ";
	getline(cin, Client.Name);

	cout << "Enter Number Phone? ";
	getline(cin, Client.PhoneNumber);

	cout << "Enter Account Balance? ";
	cin >> Client.AccountBalance;

	return Client;
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
void AddNewClient()
{
	stInfoClient Client;
	Client = ReadNewClient();
	AddDataLineToFile(ClientFileName, ConvertRecordToLine(Client));
}
void AddClient()
{
	char AddMore = 'Y';

	do
	{
		//system("cls");

		cout << "Add Data Of Client :" << endl;
		AddNewClient();

		cout << "\nClient Added Successfully, do you want to add more clients? Y/N? ";
		cin >> AddMore;
		cin.ignore();

	} while (toupper(AddMore) == 'Y');
}
void ShowAddNewClient()
{
	cout << "\n------------------------------------------\n";
	cout << "\tAdd New Clients Screen";
	cout << "\n------------------------------------------\n";

	AddClient();
}

//Delete Client : 
bool FindClientByAccountNumber(string AccountNumber, vector<stInfoClient> vClient, stInfoClient& Client)
{
	for (stInfoClient C : vClient)
	{
		if (C.AccountNumber == AccountNumber)
		{
			Client = C;
			return true;
		}
	}
	return false;
}
bool MarkClientForDeleteByAccountNumber(string AccountNumber, vector<stInfoClient>& vClient)
{
	for (stInfoClient& C : vClient)
	{
		if (C.AccountNumber == AccountNumber)
		{
			C.MarkForDelete = true;
			return true;
		}
	}
	return false;
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
bool DeleteClientByAccountNumber(string AccountNumber, vector<stInfoClient> vClient)
{
	stInfoClient Client;
	char Answer = 'n';

	if (FindClientByAccountNumber(AccountNumber, vClient, Client))
	{
		PrintClientCard(Client);

		cout << "\n\nAre you sure you want delete this client? y/n ? ";
		cin >> Answer;

		if (Answer == 'y' || Answer == 'Y')
		{
			MarkClientForDeleteByAccountNumber(AccountNumber, vClient);
			SaveClientDataToFile(ClientFileName, vClient);

			vClient = LoadClientDataFromFile(ClientFileName);

			cout << "\n\nClient Deleted Successfully.";
			return true;
		}
	}
	else
	{
		cout << "\nClient with Account Number (" << AccountNumber << ") is Not Found!\n";
		return false;
	}

}
void ShowDeleteClientScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\tDelete Client Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = ReadClientAccountNumber();
	DeleteClientByAccountNumber(AccountNumber, vClient);

}

//Update Client Info :
stInfoClient ChangeClientRecord(string AccountNumber)
{
	stInfoClient Client;

	Client.AccountNumber = AccountNumber;

	cout << "\n\nEnter Pin Code? ";
	getline(cin >> ws, Client.PinCode);

	cout << "Enter Name? ";
	getline(cin, Client.Name);

	cout << "Enter Number Phone? ";
	getline(cin, Client.PhoneNumber);

	cout << "Enter Account Balance? ";
	cin >> Client.AccountBalance;

	return Client;
}
bool UpdateClientByAccountNumber(string AccountNumber, vector<stInfoClient>& vClient)
{
	stInfoClient Client;
	char Answer = 'n';

	if (FindClientByAccountNumber(AccountNumber, vClient, Client))
	{
		PrintClientCard(Client);

		cout << "\n\nAre you sure you want update this client? y/n ? ";
		cin >> Answer;

		if (Answer == 'y' || Answer == 'Y')
		{
			for (stInfoClient& C : vClient)
			{
				if (C.AccountNumber == AccountNumber)
				{
					C = ChangeClientRecord(AccountNumber);
					break;
				}
			}

			SaveClientDataToFile(ClientFileName, vClient);

			cout << "\n\nClient Update Successfully.";
			return true;
		}
	}
	else
	{
		cout << "\nClient with Account Number (" << AccountNumber << ") is Not Found!\n";
		return false;
	}

}
void ShowUpdateClientScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\tUpdate Client Info Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = ReadClientAccountNumber();
	UpdateClientByAccountNumber(AccountNumber, vClient);

}

//Find Client :
void ShowFindClientScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\tFind Client Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	stInfoClient Client;
	string AccountNumber = ReadClientAccountNumber();
	if (FindClientByAccountNumber(AccountNumber, vClient, Client))
	{
		PrintClientCard(Client);
	}
	else
	{
		cout << "\nClient with Account Number (" << AccountNumber << ") is Not Found!\n";
	}
}

//Deposit : 
bool DepositBalanceToClientByAccountNumber(string AccountNumber, double Amount, vector <stInfoClient>& vClients)
{


	char Answer = 'n';


	cout << "\n\nAre you sure you want perfrom this transaction? y/n ? ";
	cin >> Answer;
	if (Answer == 'y' || Answer == 'Y')
	{

		for (stInfoClient& C : vClients)
		{
			if (C.AccountNumber == AccountNumber)
			{
				C.AccountBalance += Amount;
				SaveClientDataToFile(ClientFileName, vClients);
				cout << "\n\nDone Successfully. New balance is: " << C.AccountBalance;

				return true;
			}

		}


		return false;
	}

}
void ShowDepositScreen()
{
	cout << "\n-----------------------------------\n";
	cout << "\tDeposit Screen";
	cout << "\n-----------------------------------\n";

	stInfoClient Client;

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = ReadClientAccountNumber();

	while (!FindClientByAccountNumber(AccountNumber, vClient, Client))
	{
		cout << "\nClient with [" << AccountNumber << "] Not Found, Enter another Account Number? ";
		getline(cin >> ws, AccountNumber);
	}

	PrintClientCard(Client);

	double Amount = 0;
	cout << "\nPlease enter deposit amount? ";
	cin >> Amount;

	DepositBalanceToClientByAccountNumber(AccountNumber, Amount, vClient);
}

//Withdraw :
void ShowWithdrawScreen()
{
	cout << "\n-----------------------------------\n";
	cout << "\tWithdraw Screen";
	cout << "\n-----------------------------------\n";

	stInfoClient Client;

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	string AccountNumber = ReadClientAccountNumber();

	while (!FindClientByAccountNumber(AccountNumber, vClient, Client))
	{
		cout << "\nClient with [" << AccountNumber << "] Not Found, Enter another Account Number? ";
		getline(cin >> ws, AccountNumber);
	}

	PrintClientCard(Client);

	double Amount = 0;
	cout << "\nPlease enter withdraw amount? ";
	cin >> Amount;

	while (Amount > Client.AccountBalance)
	{
		cout << "\nAmount Exceeds the balance, you can withdraw up to : " << Client.AccountBalance << endl;
		cout << "Please enter another amount? ";
		cin >> Amount;
	}

	DepositBalanceToClientByAccountNumber(AccountNumber, Amount * (-1), vClient);
}

//Total Balance :
void PrintClientRecordToTotalBalance(stInfoClient Client)
{
	cout << "| " << setw(15) << left << Client.AccountNumber;
	cout << "| " << setw(40) << left << Client.Name;
	cout << "| " << setw(12) << left << Client.AccountBalance;
}
void ShowTotalBalance()
{
	cout << "\n-----------------------------------\n";
	cout << "\tTotal Screen Screen";
	cout << "\n-----------------------------------\n";

	vector<stInfoClient> vClient = LoadClientDataFromFile(ClientFileName);
	double TotalBalance = 0;

	cout << "\n\t\t\t\t\t\tClient List (" << vClient.size() << ") Client(S).";
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";
	cout << "| " << left << setw(15) << "AccountNumber";
	cout << "| " << left << setw(40) << "Name";
	cout << "| " << left << setw(12) << "AccountBalance";
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

	if (vClient.size() == 0)
	{
		cout << "\t\t\t\tNo Clients Available In the System!";
	}
	else
	{
		for (stInfoClient Client : vClient)
		{
			TotalBalance += Client.AccountBalance;
			PrintClientRecordToTotalBalance(Client);
			cout << endl;
		}
	}
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

	cout << setw(60) << right << "Total Balance : " << TotalBalance << endl;
}



void ShowEndScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\tProgram End Screen";
	cout << "\n------------------------------------------\n";
}
void GoBackToMainMenue()
{
	cout << "\n\nPress any key to go back to main menue...";
	system("pause > 0");
	ShowMainMenue();
}
void GoBackToTransactionMenue()
{
	cout << "\n\nPress any key to go back to main menue...";
	system("pause > 0");
	ShowTransactionMenueScreen();
}
short ReadMainMenueOption()
{
	short Choice;

	cout << "Choice what do you want to do? [1 to 7]? ";
	cin >> Choice;

	return Choice;
}
short ReadTransactionMenueOption()
{
	short Choice;

	cout << "Choice what do you want to do? [1 to 4]? ";
	cin >> Choice;

	return Choice;
}
void PerformTransactionMenueOption(enTransactionMenueOption TransactionMenueOption)
{
	switch (TransactionMenueOption)
	{
	case enTransactionMenueOption::eDeposit:
	{
		system("cls");
		ShowDepositScreen();
		GoBackToTransactionMenue();
		break;
	}
	case enTransactionMenueOption::eWithdraw:
	{
		system("cls");
		ShowWithdrawScreen();
		GoBackToTransactionMenue();
		break;
	}
	case enTransactionMenueOption::eTotalBalance:
	{
		system("cls");
		ShowTotalBalance();
		GoBackToTransactionMenue();
		break;

	}
	case enTransactionMenueOption::eMainMenue:
	{
		ShowMainMenue();
		break;
	}
	}
}
void PerformMainMenueOption(enMainMenueOption MainMenueOption)
{

	switch (MainMenueOption)
	{
	case enMainMenueOption::eShowClientList:
	{
		system("cls");
		ShowAllClientScreen();
		GoBackToMainMenue();
		break;
	}
	case enMainMenueOption::eAddClientToSystem:
	{
		system("cls");
		ShowAddNewClient();
		GoBackToMainMenue();
		break;
	}
	case enMainMenueOption::eDeleteClient:
	{
		system("cls");
		ShowDeleteClientScreen();
		GoBackToMainMenue();
		break;
	}
	case enMainMenueOption::eUpdateClientInfo:
	{
		system("cls");
		ShowUpdateClientScreen();
		GoBackToMainMenue();
		break;
	}
	case enMainMenueOption::eFindClient:
	{
		system("cls");
		ShowFindClientScreen();
		GoBackToMainMenue();
		break;
	}
	case enMainMenueOption::eTransaction:
	{
		ShowTransactionMenueScreen();
		break;
	}
	case enMainMenueOption::eExit:
	{
		system("cls");
		ShowEndScreen();
		break;
	}
	};
}
void ShowTransactionMenueScreen()
{
	system("cls");

	cout << "================================================================================================\n\n";
	cout << "                                Transactions Menue Screan                                       \n\n";
	cout << "================================================================================================\n";
	cout << "       [ 1 ] Deposit" << endl;
	cout << "       [ 2 ] Withdraw" << endl;
	cout << "       [ 3 ] Total Balance" << endl;
	cout << "       [ 4 ] Main Menue" << endl;
	cout << "================================================================================================\n\n";
	PerformTransactionMenueOption((enTransactionMenueOption)ReadTransactionMenueOption());
}
void ShowMainMenue()
{
	system("cls");

	cout << "================================================================================================\n\n";
	cout << "                                        Main Menue Screan                                       \n\n";
	cout << "================================================================================================\n";
	cout << "       [ 1 ] Show Client List" << endl;
	cout << "       [ 2 ] Add New Client" << endl;
	cout << "       [ 3 ] Delete Client" << endl;
	cout << "       [ 4 ] Update Client Info" << endl;
	cout << "       [ 5 ] Find Clint" << endl;
	cout << "       [ 6 ] Transaction" << endl;
	cout << "       [ 7 ] Exit" << endl;
	cout << "================================================================================================\n\n";
	PerformMainMenueOption((enMainMenueOption)ReadMainMenueOption());

}

int main()
{
	ShowMainMenue();

	return 0;
}

```
