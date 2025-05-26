# KBC-Game
#include <cstdlib>
#include <ctime>
#include <iostream>
#include <limits>
#include <vector>

using namespace std;

// Function prototypes

void displayWelcomeMessage();
int askQuestion(int questionSet,
                const vector<vector<string> >& questions,
                int);
void displayLifelines(int lifelinesUsed);
void fiftyFifty(vector<string>& options, int correctOption);
void flipTheQuestion(int& questionIndex);
void provideHint(vector<vector<string> >&, int);
int getOption(const vector<vector<string> >& Question,
              int currentIndex);

int main()
{
    srand(time(0));

    int totalAmount = 0;
    // amount changes as the number of questions increases
    int questionSetAmounts[]
        = { 1000,  4000,  8000,  15000, 25000,
            40000, 60000, 75000, 90000, 100000 };

    // question bank
    vector<vector<string> > questions = {
        { "What is the capital of France?", "A. Paris",
          "B. Berlin", "C. Rome", "D. Madrid", "A" },
        { "Which planet is known as the Red Planet?",
          "A. Mars", "B. Venus", "C. Jupiter", "D. Saturn",
          "A" },
        { "What is the largest mammal?", "A. Elephant",
          "B. Blue Whale", "C. Giraffe", "D. Lion", "B" },
        { "Which of the following is observed as Sports "
          "Day every year?",
          "A. 22nd April", "B. 26th  july",
          "C. 29th August", "D. 2nd October", "D: Apr 28",
          "A" },
        { "Pongal is a popular festival of which state?",
          "A. Karnataka", "B. Kerala", "C. Tamil Nadu",
          "D. Andhra Pradesh", "C" },
        { "The International Literacy Day is observed on:",
          "A. Sep 8", "B. Nov 28", "C. May 2", "D. Sep 22",
          "A" },
        { "Which of the-following is wrongly matched?",
          "A. Qutab Minar- Delhi", "B. Taj Mahal -Agra",
          "C. Ajanta Caves -Maharashtra",
          "D. Charminar -Lucknow", "D" },
        { "The 227 year old 'Nawab Saheb Ki Haveli' is "
          "Iocated at",
          "A. Hyderabad", "B. Jaipur", "C. New Delhi",
          "D. Agra", "B" },
        { "Who is the current CEO of Google?",
          "A. Sundar Pichai", "B. Satya Nadela",
          "C. Mahesh", "D. Pintu", "A" },
        { "How many kgs are there in a 1 tonne", "A. 100",
          "B. 1000", "C. 10", "D. 10000", "B" }
    };

    // total number of available questions
    int numsOfQuestions = questions.size();

    // lifeline and used question
    int lifelinesUsed = 0;
    bool usedTracker[10] = { false };

    displayWelcomeMessage();

    int flag = 1;
    int i = 0;

    // main loop
    for (int loop = 0; loop < numsOfQuestions; ++loop) {
        // selecting a random question
        if (flag) {
            while (usedTracker[i]) {
                i = rand() % 10;
            }
        }
        flag = 1;
        int amount = questionSetAmounts[loop];
        // asking question
        int correctOption
            = tolower(askQuestion(i, questions, i));

        char wantToUseLifeline;

        // asking choice
        char userChoice;
        cout << "Enter your choice (A/B/C/D), Enter (L) "
                "for lifeline: ";
        cin >> userChoice;
        userChoice = tolower(userChoice);

        // lifeline logic
        if (userChoice == 'l' && lifelinesUsed < 3) {

            displayLifelines(lifelinesUsed);
            int lifelineChoice;
            cout << "Enter your lifeline choice: ";
            cin >> lifelineChoice;

            switch (lifelineChoice) {
            case 1:
                fiftyFifty(questions[i], correctOption);
                lifelinesUsed++;
                loop--;
                flag = 0;
                continue;
            case 2:
                flipTheQuestion(i);
                lifelinesUsed++;
                loop--;
                flag = 1;
                continue;
            case 3:
                provideHint(questions, i);
                lifelinesUsed++;
                loop--;
                flag = 0;
                continue;
            default:
                cout << "Invalid lifeline choice. "
                        "Proceeding without using lifeline."
                     << endl;
                lifelinesUsed++;
                loop--;
                flag = 0;
                continue;
            }
        }

        // options logic
        if (userChoice == correctOption) {
            totalAmount += amount;
            cout << "Correct! You've won $" << totalAmount
                 << endl;
            usedTracker[i] = true;
        }
        else {
            cout << "Incorrect! You won $" << totalAmount
                 << endl;
            break;
        }
    }

    cout << "Thanks for playing KBC! You won a total of $"
         << totalAmount << endl;

    return 0;
}

// function to find the index of correct option
int getOption(const vector<vector<string> >& Question,int currentIndex)
{
    return (Question[currentIndex][5][0]) - 64;
}

// displaying welcome message
void displayWelcomeMessage()
{
    cout << "Welcome to Kaun Banega Crorepati (KBC)!"
         << endl;
    cout << "Answer the following questions to win big!"
         << endl;
    cout << "You can use lifelines to help you along the "
            "way."
         << endl
         << endl;
}

// printing quetion and returning correct option
int askQuestion(int questionSet,
                const vector<vector<string> >& questions,
                int currentIndex)
{
    cout << "\nQ" << currentIndex + 1 << " "
         << questions[questionSet][0] << endl;
    for (int i = 1; i <= 4; ++i) {
        cout << questions[questionSet][i] << endl;
    }

    int correctOption = questions[questionSet][5][0];
    return correctOption;
}

void displayLifelines(int lifelinesUsed)
{
    cout << "Choose a lifeline:" << endl;
    cout << "1. 50:50 - Remove two wrong options" << endl;
    cout << "2. Flip the question - Replace the question"
         << endl;
    cout << "3. Hint - Get a hint for the question" << endl;
    cout << "Lifelines used: " << lifelinesUsed << "/3"
         << endl;
}

// 50-50 lifeline logic
void fiftyFifty(vector<string>& question, int correctOption)
{
    vector<int> wrongOptions;
    for (int i = 1; i <= 4; ++i) {
        if (question[i][0] != toupper(correctOption)) {
            wrongOptions.push_back(i);
        }
    }

    int removeOption1 = wrongOptions[rand() % 3];
    int removeOption2 = removeOption1;
    while (removeOption1 == removeOption2) {
        removeOption2 = wrongOptions[rand() % 3];
    }

    cout << "50:50 - Options " << question[removeOption1]
         << " and " << question[removeOption2]
         << " are removed.\n"
         << endl;
    question[removeOption1] = question[removeOption2] = "";

    cin.ignore(numeric_limits<streamsize>::max(),
               '\n'); // Clear newline from previous input
}

// flip the question lifeline logic
void flipTheQuestion(int& questionIndex)
{
    cout << "Flipping the question..." << endl;
    questionIndex = rand() % 10;
}

// hint provider lifeline logic
void provideHint(vector<vector<string> >& Questions,
                 int currentIndex)
{
    cout << "Hint: The correct option starts with the "
            "letter "
         << Questions[currentIndex]
                     [getOption(Questions, currentIndex)][3]
         << "." << endl;
}
