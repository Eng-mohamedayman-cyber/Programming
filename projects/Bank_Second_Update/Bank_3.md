


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
struct stInfoUser
{
	string UserName;
	string Password;
	int Permission;
	bool MarkForDelete = false;
};

const string ClientFileName = "Client.txt";
const string UserFileName = "User.txt";

stInfoUser CurrentUser;

enum enMainMenueOption { eShowClientList = 1, eAddClientToSystem = 2, eDeleteClient = 3, eUpdateClientInfo = 4, eFindClient = 5, eTransaction = 6, eManageUsers = 7, eLogout = 8 };
enum enTransactionMenueOption { eDeposit = 1, eWithdraw = 2, eTotalBalance = 3, eMainMenue = 4 };
enum enManageUser { eListUser = 1, eAddUserToSystem = 2, eDeleteUser = 3, eUpdateUserInfo = 4, eFindUser = 5, eMainMenu = 6};
enum enMainMenuPermissions { eAll = -1, pListClient = 1, pAddNewClient = 2, pDeleteClient = 4, pUpdateClients = 8, pFindClient = 16, pTransactions = 32, pManageUsers = 64};

void ShowMainMenue();
void ShowTransactionMenueScreen();
void ShowManageUserMenueScreen();
void ShowAccessDeniedMessage();
bool CheckAccessPermission(enMainMenuPermissions Permission);
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
stInfoUser ConvertLineToRecordForUser(string Line, string Seperator = "#//#")
{
	vector<string> vString = SplitString(Line, Seperator);

	stInfoUser User;

	User.UserName = vString.at(0);
	User.Password = vString.at(1);
	User.Permission = stoi(vString.at(2));

	return User;

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
string ConvertRecordToLineForUser(stInfoUser User, string Seperator = "#//#")
{
	string stUserRecord = "";

	stUserRecord += User.UserName + Seperator;
	stUserRecord += User.Password + Seperator;
	stUserRecord += to_string(User.Permission) + Seperator;
	
	return stUserRecord;
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
void PrintUserCard(stInfoUser User)
{
	cout << "\nThe following are the User details:\n";

	cout << "\n--------------------------------------------------------\n";
	cout << "User Name        :" << User.UserName << endl;
	cout << "Password         :" << User.Password << endl;
	cout << "Permission       :" << User.Permission << endl;
	cout << "--------------------------------------------------------\n";

}
string ReadClientAccountNumber()
{
	string AccountNumber;

	cout << "Please Enter The Account Number? ";
	cin >> AccountNumber;

	return AccountNumber;
}
string ReadUserName()
{
	string UserName;

	cout << "Please Enter The UserName? ";
	cin >> UserName;

	return UserName;
}
string ReadPassword()
{
	string Password;

	cout << "Please Enter The Password? ";
	cin >> Password;

	return Password;
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
bool UserExistsByUserName(string UserName, string FileName)
{
	vector<stInfoUser> vUser;
	fstream MyFlile;

	MyFlile.open(FileName, ios::in);

	if (MyFlile.is_open())
	{
		string Line;
		stInfoUser User;

		while (getline(MyFlile, Line))
		{
			User = ConvertLineToRecordForUser(Line);

			if (User.UserName == UserName)
			{
				MyFlile.close();
				return true;
			}
			vUser.push_back(User);
		}
		MyFlile.close();
	}
	return false;
}
int ReadPermissionsToSet()
{
	int permission = 0;
	char Choose = 'n';

	cout << "Do You Want To Give All Access? y/n? ";
	cin >> Choose;

	if (Choose == 'y' || Choose == 'Y')
	{
		return permission = -1;
	}
	
	cout << "\nDo you want to give access to : \n " << endl;
	cout << "\nShow Client List? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pListClient;
	}
	
	cout << "\nAdd New Client? y/n? ";
	
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pAddNewClient;
	}
	
	cout << "\nDelete Client? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pDeleteClient;
	}
	
	cout << "\nUpdate Client? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pUpdateClients;
	}

	cout << "\nFind Client? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pFindClient;
	}
		
	cout << "\nTransaction? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pTransactions;
	}
		
	cout << "\nManage Users? y/n? ";
	cin >> Choose;
	if (Choose == 'y' || Choose == 'Y')
	{
		permission += enMainMenuPermissions::pManageUsers;
	}

	return permission;
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
	if (!CheckAccessPermission(enMainMenuPermissions::pListClient))
	{
		ShowAccessDeniedMessage();
		return;
	}
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
	if (!CheckAccessPermission(enMainMenuPermissions::pAddNewClient))
	{
		ShowAccessDeniedMessage();
		return;
	}

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
	if (!CheckAccessPermission(enMainMenuPermissions::pDeleteClient))
	{
		ShowAccessDeniedMessage();
		return;
	}

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
	if (!CheckAccessPermission(enMainMenuPermissions::pUpdateClients))
	{
		ShowAccessDeniedMessage();
		return;
	}

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
	if (!CheckAccessPermission(enMainMenuPermissions::pFindClient))
	{
		ShowAccessDeniedMessage();
		return;
	}

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

//Show Users List :
vector<stInfoUser> LoadUserDataFromFile(string FileName)
{
	vector<stInfoUser> vUser;

	fstream MyFile;
	MyFile.open(FileName, ios::in);

	if (MyFile.is_open())
	{
		string Line;
		stInfoUser User;

		while (getline(MyFile, Line))
		{
			User = ConvertLineToRecordForUser(Line);

			vUser.push_back(User);
		}

		MyFile.close();
	}

	return vUser;
}
void PrintUserRecord(stInfoUser User)
{
	cout << "| " << setw(15) << left << User.UserName;
	cout << "| " << setw(20) << left << User.Password;
	cout << "| " << setw(40) << left << User.Permission;
}
void ShowAllUserScreen()
{
	vector<stInfoUser> vUser = LoadUserDataFromFile(UserFileName);

	cout << "\n\t\t\t\t\t\tClient List (" << vUser.size() << ") Client(S).";
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";
	cout << "| " << left << setw(15) << "UserName";
	cout << "| " << left << setw(20) << "Password";
	cout << "| " << left << setw(40) << "Permission";;
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

	if (vUser.size() == 0)
	{
		cout << "\t\t\t\tNo Clients Available In the System!";
	}
	else
	{
		for (stInfoUser User : vUser)
		{
			PrintUserRecord(User);
			cout << endl;
		}
	}
	cout << "\n--------------------------------------------------------------------";
	cout << "-------------------------------------------------\n";

}

//Add Users
stInfoUser ReadNewUser()
{
	stInfoUser User;
	
	cout << "\nEnter username? ";
	getline(cin >> ws, User.UserName);

	while (UserExistsByUserName(User.UserName, UserFileName))
	{
		cout << "\nUser with [" << User.UserName << "] already exists, Enter another User Name? ";
		getline(cin >> ws, User.UserName);
	}

	cout << "Enter Password? ";
	getline(cin, User.Password);

	User.Permission = ReadPermissionsToSet();

	return User;
}
void AddNewUser()
{
	stInfoUser User;
	User = ReadNewUser();
	AddDataLineToFile(UserFileName, ConvertRecordToLineForUser(User));
}
void AddUser()
{
	char AddMore = 'Y';

	do
	{

		cout << "Add Data Of User :" << endl;
		AddNewUser();

		cout << "\nUser Added Successfully, do you want to add more Users? Y/N? ";
		cin >> AddMore;
		cin.ignore();

	} while (toupper(AddMore) == 'Y');
}
void ShowAddNewUser()
{
	cout << "\n------------------------------------------\n";
	cout << "\tAdd New Users Screen";
	cout << "\n------------------------------------------\n";

	AddUser();
}

//Delete Client : 
bool FindUserByUserName(string UserName, vector<stInfoUser> vUser, stInfoUser& User)
{
	for (stInfoUser U : vUser)
	{
		if (U.UserName == UserName)
		{
			User = U;
			return true;
		}
	}
	return false;
}
bool FindUserByUserNameAndPassword(string UserName, string Password, stInfoUser& User)
{
	vector<stInfoUser> vUser = LoadUserDataFromFile(UserFileName);

	for (stInfoUser U : vUser)
	{
		if (U.UserName == UserName && U.Password == Password)
		{
			User = U;
			return true;
		}
	}
	return false;
}
bool MarkUserForDeleteByUserName(string UserName, vector<stInfoUser>& vUser)
{
	for (stInfoUser& U : vUser)
	{
		if (U.UserName == UserName)
		{
			U.MarkForDelete = true;
			return true;
		}
	}
	return false;
}
vector<stInfoUser> SaveUserDataToFile(string FileName, vector<stInfoUser> vUser)
{
	fstream MyFile;
	MyFile.open(UserFileName, ios::out);

	string DataLine;
	if (MyFile.is_open())
	{
		for (stInfoUser U : vUser)
		{
			if (U.MarkForDelete == false)
			{
				DataLine = ConvertRecordToLineForUser(U);
				MyFile << DataLine << endl;
			}
		}
		MyFile.close();
	}
	return vUser;
}
bool DeleteUserByUserName(string UserName, vector<stInfoUser> vUser)
{
	stInfoUser User;
	char Answer = 'n';

	if (FindUserByUserName(UserName, vUser, User))
	{
		if (User.UserName == "admin")
		{
			cout << "\n\ncan't delete this user name" << endl;
			return false;
		}
		PrintUserCard(User);

		cout << "\n\nAre you sure you want delete this User? y/n ? ";
		cin >> Answer;

		if (Answer == 'y' || Answer == 'Y')
		{
			MarkUserForDeleteByUserName(UserName, vUser);
			SaveUserDataToFile(UserFileName, vUser);

			vUser = LoadUserDataFromFile(UserFileName);

			cout << "\n\nUser Deleted Successfully.";
			return true;
		}
	}
	else
	{
		cout << "\nUser with User Name (" << UserName << ") is Not Found!\n";
		return false;
	}

}
void ShowDeleteUserScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\tDelete User Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoUser> vUser = LoadUserDataFromFile(UserFileName);
	string UserName = ReadUserName();
	DeleteUserByUserName(UserName, vUser);

}

//Update User
stInfoUser ChangeUserRecord(string UserName)
{
	stInfoUser User;
	char Choose = 'y';

	User.UserName = UserName;

	cout << "Enter Password? ";
	cin >> User.Password;

	User.Permission = ReadPermissionsToSet();

	return User;
}
bool UpdateUserByUserName(string UserName, vector<stInfoUser>& vUser)
{
	stInfoUser User;
	char Answer = 'n';

	if (FindUserByUserName(UserName, vUser, User))
	{
		PrintUserCard(User);

		cout << "\n\nAre you sure you want update this User? y/n ? ";
		cin >> Answer;

		if (Answer == 'y' || Answer == 'Y')
		{
			for (stInfoUser& U : vUser)
			{
				if (User.UserName == UserName)
				{
					U = ChangeUserRecord(UserName);
					break;
				}
			}

			SaveUserDataToFile(UserFileName, vUser);

			cout << "\n\nUser Update Successfully.";
			return true;
		}
	}
	else
	{
		cout << "\nUser with User Name (" << UserName << ") is Not Found!\n";
		return false;
	}

}
void ShowUpdateUserScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\t\tUpdate User Info Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoUser> vUser = LoadUserDataFromFile(UserFileName);
	string UserName = ReadUserName();
	UpdateUserByUserName(UserName, vUser);

}

//Find User :
void ShowFindUserScreen()
{
	cout << "\n------------------------------------------\n";
	cout << "\t\tFind User Screen";
	cout << "\n------------------------------------------\n";

	vector<stInfoUser> vUser = LoadUserDataFromFile(UserFileName);
	stInfoUser User;
	string UserName = ReadUserName();
	if (FindUserByUserName(UserName, vUser, User))
	{
		PrintUserCard(User);
	}
	else
	{
		cout << "\nUser with User Name (" << UserName << ") is Not Found!\n";
	}
}


void ShowEndScreen()
{
	cout << "\n-----------------------------------\n";
	cout << "\tProgram Ends :-)";
	cout << "\n-----------------------------------\n";
}
bool CheckAccessPermission(enMainMenuPermissions Permission)
{
	if (CurrentUser.Permission == enMainMenuPermissions::eAll)
		return true;

	if ((Permission & CurrentUser.Permission) == Permission)
		return true;
	else
		return false;
}
void GoBackToMainMenue()
{
	cout << "\n\nPress any key to go back to main menue...";
	system("pause > 0");
	ShowMainMenue();
}
void GoBackToTransactionMenue()
{
	cout << "\n\nPress any key to go back to transaction menue...";
	system("pause > 0");
	ShowTransactionMenueScreen();
}
void GoBackToManageUserMenue()
{
	cout << "\n\nPress any key to go back to manage user menue...";
	system("pause > 0");
	ShowManageUserMenueScreen();
}
short ReadMainMenueOption()
{
	short Choice;

	cout << "Choice what do you want to do? [1 to 8]? ";
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
short ReadManageUserMenueOption()
{
	short Choice;

	cout << "Choice what do you want to do? [1 to 6]? ";
	cin >> Choice;

	return Choice;
}
void PerformManageUserMenueOption(enManageUser ManageUserMenueOption)
{
	switch (ManageUserMenueOption)
	{
	case enManageUser::eListUser:
	{
		system("cls");
		ShowAllUserScreen();
		GoBackToManageUserMenue();
		break;
	}
	case enManageUser::eAddUserToSystem:
	{
		system("cls");
		ShowAddNewUser();
		GoBackToManageUserMenue();
		break;
	}
	case enManageUser::eDeleteUser:
	{
		system("cls");
		ShowDeleteUserScreen();
		GoBackToManageUserMenue();
		break;

	}
	case enManageUser::eUpdateUserInfo:
	{
		system("cls");
		ShowUpdateUserScreen();
		GoBackToManageUserMenue();
		break;
	}
	case enManageUser::eFindUser:
	{
		system("cls");
		ShowFindUserScreen();
		GoBackToManageUserMenue();
		break;
	}
	case enManageUser::eMainMenu:
	{
		ShowMainMenue();
		break;
	}
	}
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
	case enMainMenueOption::eManageUsers:
	{
		system("cls");
		ShowManageUserMenueScreen();
		break;
	}
	case enMainMenueOption::eLogout:
	{
		system("cls");
		Login();
		break;
	}
	};
}
void ShowTransactionMenueScreen()
{
	if (!CheckAccessPermission(enMainMenuPermissions::pTransactions))
	{
		ShowAccessDeniedMessage();
		return;
	}

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
void ShowManageUserMenueScreen()
{
	if (!CheckAccessPermission(enMainMenuPermissions::pManageUsers))
	{
		ShowAccessDeniedMessage();
		return;
	}

	system("cls");

	cout << "================================================================================================\n\n";
	cout << "                                Manage Users Menue Screan                                       \n\n";
	cout << "================================================================================================\n";
	cout << "       [ 1 ] List Users" << endl;
	cout << "       [ 2 ] Add New User" << endl;
	cout << "       [ 3 ] Delete User" << endl;
	cout << "       [ 4 ] Update User" << endl;
	cout << "       [ 5 ] Find User" << endl;
	cout << "       [ 6 ] Main Menu" << endl;
	cout << "================================================================================================\n\n";
	PerformManageUserMenueOption((enManageUser)ReadManageUserMenueOption());
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
	cout << "       [ 7 ] Manage Users" << endl;
	cout << "       [ 8 ] Logout" << endl;
	cout << "================================================================================================\n\n";
	PerformMainMenueOption((enMainMenueOption)ReadMainMenueOption());

}

//Login
bool LoadUserInfo(string UserName, string Password)
{
	if (FindUserByUserNameAndPassword(UserName, Password, CurrentUser))
		return true;
	else
		return false;
}
void Login()
{
	bool LoginFaild = false;
	string UserName, Password;

	do
	{
		system("cls");

		cout << "\n------------------------------------------\n";
		cout << "\t\tLogin Screen";
		cout << "\n------------------------------------------\n";

		if (LoginFaild)
		{
			cout << "\nInvalid Password/UserName\n" << endl;
		}

		string UserName = ReadUserName();
		string Password = ReadPassword();

		LoginFaild = !LoadUserInfo(UserName, Password);

	} while (LoginFaild);

	ShowMainMenue();
}
void ShowAccessDeniedMessage()
{
	cout << "\n------------------------------------\n";
	cout << "Access Denied, \nYou dont Have Permission To Do this, \nPlease Conact Your Admin.";
	cout << "\n------------------------------------\n";
}

int main()
{
	Login();

	return 0;
}

```
