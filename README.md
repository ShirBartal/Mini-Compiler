# Mini-Compiler
#include <iostream>
#include <stack>
#include <fstream>
#include <string>
#include <iomanip>
#include <cassert>
#include <sstream>
using namespace std;
void printFile(istream &dictfile);
bool balanceFile(istream &dictfile);
bool Matching(char, char);
void getChar(char& c, istream&infile, int& i);

struct charAndLine
{
	int line;
	char openCharacter;
};
int main()
{
	//checkings();
	ifstream infile;
	string filename;
	cout << "Please enter filename for C++ code: ";
	cin >> filename;
	infile.open(filename.c_str());
	if (!infile) {
		cout << "File not found!" << endl;
		return (1);
	}
	printFile(infile);
	if (balanceFile(infile))
		cout << "balance ok" << endl;
	else
		cout << "Balance baaaaadddddd";
}

void printFile(istream& infile)
{
	int i = 1;
	string a;
	while (infile.peek() != EOF)
	{
		cout << i << "  ";
		getline(infile, a);
		cout << setw(3) << a << endl;
		i++;
	}
	infile.clear();
	infile.seekg(0, infile.beg);
}

bool balanceFile(istream &infile) {
	stack <charAndLine> stack1;
	char c, d, e, f;
	bool match = true;
	int numberOfLine = 1;
	while (infile.peek() != EOF)
	{
		//infile.get(c);
		getChar(c, infile, numberOfLine);
		if (c == '/'&&infile.peek() == '*')
		{
			infile.get(c); //this must be a *
			int matchLine = numberOfLine;
			getChar(c, infile, numberOfLine); //this is what we're checking
			while ((c != '*' || infile.peek() != '/') && infile.peek() != EOF)
			{
				getChar(c, infile, numberOfLine);
			}
			if (infile.peek() == EOF)
			{
				match = false;
				cout << "unmatched /* symbol on line " << matchLine << endl;
				break;
			}
			cout << "pair matching /* and */" << endl;
		}
		else
		{
			if (c == '/'&&infile.peek() == '/')
			{
				string newLine;
				getline(infile, newLine);
			}
		}

		if (c == '"' || c == '\'') {
			char save;
			save = c;
			getChar(c, infile, numberOfLine);
			while (c != save && infile.peek() != EOF) {
				getChar(c, infile, numberOfLine);
				if (c == '\n') {
					match = false;
					cout << "unmatched " << save << " symbol on line " << numberOfLine << endl;
					break;
				}
			}
			if (c != save && infile.peek() == EOF && infile.peek() != '\n') {
				match = false;
				cout << "unmatched " << save << " symbol on line " << numberOfLine << endl;
				break;
			}
			cout << "pair matching " << save << " and " << save << endl;
		}
		if (c == '(' || c == '{' || c == '[') {
			charAndLine a;
			a.line = numberOfLine;
			a.openCharacter = c;
			stack1.push(a);
		}
		else if (c == ')' || c == '}' || c == ']')
		{
			if (stack1.empty())
			{
				cout << "unmatched " << c << " symbol on line " << numberOfLine << endl;
				match = false;
				break;
			}
			charAndLine openChar;
			openChar = stack1.top();
			stack1.pop();
			if (!Matching(openChar.openCharacter, c))
			{
				match = false;
				cout << "unmatched " << openChar.openCharacter << " symbol on line " << openChar.line << endl;
				break;
			}
			else
				cout << "pair matching " << openChar.openCharacter << " and " << c << endl;
		}
	}
	if (!stack1.empty() && match == true) {
		cout << "unmatched " << stack1.top().openCharacter << " symbol on line " << stack1.top().line << endl;
		match = false;
	}
	return match;
}

bool Matching(char a, char b)
{
	if (a == '{') {
		if (b == '}')
			return true;
		else
			return false;
	}
	if (a == '(') {
		if (b == ')')
			return true;
		else
			return false;
	}
	if (a == '[') {
		if (b == ']')
			return true;
		else
			return false;
	}
	return false;
}

void getChar(char& c, istream&infile, int& i)
{
	infile.get(c);
	if (c == '\n')
		i++;
}



