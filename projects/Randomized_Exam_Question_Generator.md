```c
#include <iostream>
#include<cstdlib>
#include<ctime>
using namespace std;

enum enQuestionLevel { EasyLevel = 1 , MedLevel = 2 , HardLevel = 3 , Mix = 4 };
enum enOperationType { Add = 1 , Sub = 2 , Mul = 3 , divi = 4 , MixOp = 5 };

struct stQuestion
{
    int Number1 = 0;
    int Number2 = 0;
    enQuestionLevel QuestionLevel;
    enOperationType OperationType;
    int CorrectAnswer = 0;
    int PlayerAnswer = 0;
    bool AnswerResult = false;
};
struct stQuizz
{
    stQuestion QuestionList[100];
    short NumberOfQuestion = 0;
    enQuestionLevel QuestionLevel;
    enOperationType OperationType;
    short NumberOfWrongAnswer = 0;
    short NumberOfRightAnswer = 0;
    bool isPass = false;
};

int ReadHowManyQuestion()
{
    int NumberOfQuestion = 0;

    do
    {
        cout<<"How many question do you want to answer? ";
        cin>>NumberOfQuestion;
    }while(NumberOfQuestion < 1 || NumberOfQuestion > 10);

    return NumberOfQuestion;
}
enQuestionLevel ReadQuestionLevel()
{
    short QuestionLevel = 0;

    do
    {
        cout<<"Enter Question Level [1] Easy, [2] Med, [3] Hard, [4] Mix ? ";
        cin>>QuestionLevel;
    }while(QuestionLevel < 1 || QuestionLevel > 4);

    return (enQuestionLevel)QuestionLevel;
}
enOperationType ReadOpType()
{
    short OpType = 0;

    do
    {
        cout<<"Enter Question Type [1] Add, [2] Sub, [3] Mul, [4] Div, [5] Mix ? ";
        cin>>OpType;
    }while(OpType < 1 || OpType > 5);

    return (enOperationType)OpType;
}
int RandomNumber(int From , int To)
{
    int RanNum = rand() % (To - From + 1 ) + From ;
    return RanNum;
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
void ResetScreen()
{
    system("cls");
    system("color 0F");
}
int SimpleCalculator(int Number1 , int Number2 , enOperationType OpType)
{
    switch(OpType)
    {
    case enOperationType::Add :
        return Number1 + Number2;
    case enOperationType::Sub :
        return Number1 - Number2;
    case enOperationType::Mul :
        return Number1 * Number2;
    case enOperationType::divi :
        return Number1 / Number2;
    default:
        return Number1 + Number2;
    }
}
enOperationType GetRandomOperationType()
{
    int OP = RandomNumber(1,4);
    return (enOperationType)OP;
}
stQuestion GenerateQuestion(enQuestionLevel QuestionLevel,enOperationType OpType)
{
    stQuestion Question;

    if(QuestionLevel == enQuestionLevel::Mix)
    {
        QuestionLevel = (enQuestionLevel)RandomNumber(1,3);
    }
    if(OpType == enOperationType::MixOp)
    {
        OpType = GetRandomOperationType();
    }

    Question.OperationType = OpType;

    switch(QuestionLevel)
    {
    case enQuestionLevel::EasyLevel :
        Question.Number1 = RandomNumber(1,10);
        Question.Number2 = RandomNumber(1,10);

        Question.CorrectAnswer = SimpleCalculator(Question.Number1,Question.Number2,Question.OperationType);
        Question.QuestionLevel = QuestionLevel;
        return Question;
    case enQuestionLevel::MedLevel :
        Question.Number1 = RandomNumber(10,50);
        Question.Number2 = RandomNumber(10,50);

        Question.CorrectAnswer = SimpleCalculator(Question.Number1,Question.Number2,Question.OperationType);
        Question.QuestionLevel = QuestionLevel;
        return Question;
    case enQuestionLevel::HardLevel:
        Question.Number1 = RandomNumber(50,100);
        Question.Number2 = RandomNumber(50,100);

        Question.CorrectAnswer = SimpleCalculator(Question.Number1,Question.Number2,Question.OperationType);
        Question.QuestionLevel = QuestionLevel;
        return Question;
    }

    return Question;
}
void GenerateQuizzQuestions(stQuizz& Quizz)
{
    for(short Question = 0 ; Question < Quizz.NumberOfQuestion ; Question++)
    {
        Quizz.QuestionList[Question] = GenerateQuestion(Quizz.QuestionLevel,Quizz.OperationType);
    }
}
string GetOpTypeSymbol(enOperationType OpType)
{
    switch(OpType)
    {
    case enOperationType::Add :
        return "+";
    case enOperationType::Sub :
        return "-";
    case enOperationType::Mul :
        return "*";
    case enOperationType::divi :
        return "/";
    default:
        return "Mix";
    }
}
void PrintTheQuestion(stQuizz& Quizz , short QuestionNumber)
{
    cout<<"\n";
    cout<<"Question ["<<QuestionNumber+1<<"/"<<Quizz.NumberOfQuestion<<"] \n\n";
    cout<<Quizz.QuestionList[QuestionNumber].Number1<<endl;
    cout<<Quizz.QuestionList[QuestionNumber].Number2<<" ";
    cout<<GetOpTypeSymbol(Quizz.QuestionList[QuestionNumber].OperationType);
    cout<<"\n____________"<<endl;
}
int ReadQuestionAnswer()
{
    int PlayerAnswer = 0;
    cin>>PlayerAnswer;
    return PlayerAnswer;
}
void SetScreenColor(bool Right)
{
    if(Right)
    {
        system("color 2F");
    }
    else
    {
        system("color 4F");
        cout<<"\a";
    }
}
void CorrectTheQuestionAnswer(stQuizz& Quizz , short QuestionNumber)
{
    if(Quizz.QuestionList[QuestionNumber].PlayerAnswer != Quizz.QuestionList[QuestionNumber].CorrectAnswer)
    {
        Quizz.QuestionList[QuestionNumber].AnswerResult = false;
        Quizz.NumberOfWrongAnswer++;

        cout<<"\nWrong Answer :-("<<endl;
        cout<<"The Right Answer is :";
        cout<<Quizz.QuestionList[QuestionNumber].CorrectAnswer;
        cout<<endl;
    }
    else
    {
        Quizz.QuestionList[QuestionNumber].AnswerResult = true;
        Quizz.NumberOfRightAnswer++;

        cout<<"\nRight Answer :-)"<<endl;
    }
    cout<<endl;

    SetScreenColor(Quizz.QuestionList[QuestionNumber].AnswerResult);
}
void AskAndCorrectQuestionListAnswer(stQuizz& Quizz)
{
    for(short QuestionNumber = 0 ; QuestionNumber < Quizz.NumberOfQuestion ; QuestionNumber++)
    {
        PrintTheQuestion(Quizz,QuestionNumber);
        Quizz.QuestionList[QuestionNumber].PlayerAnswer = ReadQuestionAnswer();
        CorrectTheQuestionAnswer(Quizz,QuestionNumber);
    }

    Quizz.isPass = (Quizz.NumberOfRightAnswer >= Quizz.NumberOfWrongAnswer);
}
string GetFinalResultsText(bool Pass)
{
    if(Pass)
        return "Pass :-)";
    else
        return "False :-(";
}
string GetQuestionLevelText(enQuestionLevel QuestionLevel)
{
    string arrQuestionLevelText[4] = {"Easy" , "Med" , "Hard" , "Mix"};
    return arrQuestionLevelText[QuestionLevel - 1];
}
void PrintQuizzResult(stQuizz Quizz)
{
    cout<<"\n";
    cout<<"______________________________________\n\n";
    cout<<" Final Results is "<<GetFinalResultsText(Quizz.isPass)<<endl;
    cout<<"______________________________________\n\n";

    cout<<"Number Of Question : "<<Quizz.NumberOfQuestion<<endl;
    cout<<"Question Level     : "<<GetQuestionLevelText(Quizz.QuestionLevel)<<endl;
    cout<<"OpType             : "<<GetOpTypeSymbol(Quizz.OperationType)<<endl;
    cout<<"Number Of Right Answer : "<<Quizz.NumberOfRightAnswer<<endl;
    cout<<"Number Of Wrong Answer : "<<Quizz.NumberOfWrongAnswer<<endl;
    cout<<"______________________________________\n";
}
void PlayMathGame()
{
    stQuizz Quizz;

    Quizz.NumberOfQuestion = ReadHowManyQuestion();
    Quizz.QuestionLevel = ReadQuestionLevel();
    Quizz.OperationType = ReadOpType();

    GenerateQuizzQuestions(Quizz);
    AskAndCorrectQuestionListAnswer(Quizz);

    PrintQuizzResult(Quizz);
}
void StartGame()
{
    char PlayAgain = 'Y';

    do
    {
        ResetScreen();
        PlayMathGame();

        cout<<"Do you want to play again? Y/N? ";
        cin>>PlayAgain;
    }while(PlayAgain == 'Y' || PlayAgain == 'y');

}
int main()
{
    srand((unsigned)time(NULL));

    StartGame();

    return 0;
}

```
