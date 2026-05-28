```c
#include <iostream>
#include<cstdlib>
#include<ctime>
using namespace std;

enum enGameChoice { Stone = 1 , Paper = 2 , Scissors = 3};
enum enWinner { Player1 = 1 , Computer = 2 , Draw = 3};

struct stRoundInfo{
    short RoundNumber = 0 ;
    enGameChoice Player1Choice;
    enGameChoice ComputerChoice;
    enWinner Winner;
    string WinnerName;
};
struct stGameResults{
    short GameRounds = 0;
    short Player1WinTimes = 0;
    short Computer2WinTimes = 0;
    short DrawTimes = 0;
    enWinner GameWinner ;
    string WinnerName = "";
};

int RandomNumber(int From , int To)
{
    int RanNum = rand() % (To - From + 1 ) + From ;
    return RanNum;
}
string WinnerName( enWinner Winner)
{
    string arrWinnerName[3] = { "Player" , "Computer" , "Draw"};
    return arrWinnerName[Winner - 1];
}
string ChoiceName( enGameChoice Choice)
{
    string arrChoiceName[3] = { "Stone" , "Paper" , "Scissors"};
    return arrChoiceName[Choice - 1];
}
enWinner WhoWonTheRound(stRoundInfo RoundInfo)
{
    if(RoundInfo.Player1Choice == RoundInfo.ComputerChoice)
    {
        return enWinner::Draw;
    }
    switch(RoundInfo.Player1Choice)
    {
    case enGameChoice::Stone:
        if(RoundInfo.ComputerChoice == enGameChoice::Paper)
        {
            return enWinner::Computer;
        }
        break;
    case enGameChoice::Paper:
        if(RoundInfo.ComputerChoice == enGameChoice::Scissors)
        {
            return enWinner::Computer;
        }
        break;
    case enGameChoice::Scissors:
        if(RoundInfo.ComputerChoice == enGameChoice::Stone)
        {
            return enWinner::Computer;
        }
        break;
    }
    return enWinner::Player1;
}
void SetWinnerScreenColor(enWinner Winner)
{
    switch(Winner)
    {
    case enWinner::Player1:
        system("color 2F");
        break;
    case enWinner::Computer:
        system("color 4F");
        cout<<"\a";
        break;
    case enWinner::Draw:
        system("color 6F");
        break;
    }
}
void PrintRoundResults(stRoundInfo RoundInfo)
{
    cout<<"\n____________________Round ["<<RoundInfo.RoundNumber<<"]____________________\n\n";
    cout<<"Player1  Choice : "<<ChoiceName(RoundInfo.Player1Choice)<<endl;
    cout<<"Computer Choice : "<<ChoiceName(RoundInfo.ComputerChoice)<<endl;
    cout<<"Round Winner    : ["<<RoundInfo.WinnerName<<"]"<<endl;
    cout<<"________________________________________________\n"<<endl;

    SetWinnerScreenColor(RoundInfo.Winner);
}
enWinner WhoWonTheGame(short Player1WinTimes , short ComputerWinTimes)
{
    if(Player1WinTimes > ComputerWinTimes)
        return enWinner::Player1;
    else if(ComputerWinTimes > Player1WinTimes)
        return enWinner::Computer;
    else
        return enWinner::Draw;

}
string Taps(short NumOfTaps)
{
    string t = "";
    for(int i = 0 ; i <= NumOfTaps ; i++ )
    {
        t = t + "\t";
        cout<<t;
    }
    return t ;
}
stGameResults FillGameResults(int GameRound , short Player1WinTimes , short ComputerWinTimes , short DrawTimes)
{
    stGameResults GameResults;

    GameResults.GameRounds = GameRound;
    GameResults.Player1WinTimes = Player1WinTimes;
    GameResults.Computer2WinTimes = ComputerWinTimes;
    GameResults.DrawTimes = DrawTimes;
    GameResults.GameWinner = WhoWonTheGame(Player1WinTimes,ComputerWinTimes);
    GameResults.WinnerName = WinnerName(GameResults.GameWinner);

    return GameResults;
}
enGameChoice ReadPlayerChoice()
{
    short Choice = 1;

    do
    {
        cout<<"\nYour Choice : [1]:Stone , [2]:Paper , [3]:Scissors? ";
        cin>>Choice;
    }while( Choice < 1 || Choice > 3);

    return (enGameChoice)Choice;
}
enGameChoice GetComputerChoice()
{
    return (enGameChoice)RandomNumber(1,3);
}
stGameResults PlayGame(short HowManyRounds)
{
    stRoundInfo  RoundInfo;
    short Player1WinTimes = 0, ComputerWinTimes = 0, DrawTimes = 0;
    for(short GameRound = 1 ; GameRound <= HowManyRounds ; GameRound++)
    {
        cout<<"\nRound ["<<GameRound<<"] begins:\n";
        RoundInfo.RoundNumber = GameRound;
        RoundInfo.Player1Choice = ReadPlayerChoice();
        RoundInfo.ComputerChoice = GetComputerChoice();
        RoundInfo.Winner = WhoWonTheRound(RoundInfo);
        RoundInfo.WinnerName = WinnerName(RoundInfo.Winner);

        if(RoundInfo.Winner == enWinner::Player1)
            Player1WinTimes++;
        else if(RoundInfo.Winner == enWinner::Computer)
            ComputerWinTimes++;
        else
            DrawTimes++;

        PrintRoundResults(RoundInfo);
    }
    return FillGameResults(HowManyRounds,Player1WinTimes,ComputerWinTimes,DrawTimes);
}
void ShowGameOverScreen()
{
    cout<<Taps(2)<<"______________________________________________________________\n\n";
    cout<<Taps(2)<<"                     +++ G a m e O v e r +++                  \n";
    cout<<Taps(2)<<"______________________________________________________________\n\n";

}
void ShowFinalGameResults(stGameResults GameResult)
{
    cout<<Taps(2)<<"_________________________[ Game Results ]_____________________\n\n";
    cout<<Taps(2)<<"Game Rounds        : "<<GameResult.GameRounds<<endl;
    cout<<Taps(2)<<"Player1 Won Times  : "<<GameResult.Player1WinTimes<<endl;
    cout<<Taps(2)<<"Computer Won Times : "<<GameResult.Computer2WinTimes<<endl;
    cout<<Taps(2)<<"Draw Times         : "<<GameResult.DrawTimes<<endl;
    cout<<Taps(2)<<"Final Winner       : "<<GameResult.WinnerName<<endl;
    cout<<Taps(2)<<"____________________________________________________________\n\n";

    SetWinnerScreenColor(GameResult.GameWinner);
}
short ReadHowManyRounds()
{
    short GameRounds = 1;

    do
    {
        cout<<"How Many Rounds 1 to 10 ? \n";
        cin>>GameRounds;
    }while(GameRounds < 1 || GameRounds > 10);

    return GameRounds;
}
void ResetScreen()
{
    system("cls");
    system("color 0F");
}
void StartGame()
{
    char PlayAgain = 'y';

    do
    {
        ResetScreen();
        stGameResults GameResult = PlayGame(ReadHowManyRounds());
        ShowGameOverScreen();
        ShowFinalGameResults(GameResult);

        cout<<endl<<Taps(3)<<"Do you want to play again? Y/N? ";
        cin>>PlayAgain;
        
    }while(PlayAgain == 'y' || PlayAgain == 'Y');
}
int main()
{
    srand((unsigned)time(NULL));

    StartGame();

    return 0;
}
```
