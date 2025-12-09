#include <iostream>
#include <fstream>
#include <iomanip>
using namespace std;

struct StudentsData
{
int StudentID;
char StudentName[20];
double GPA;
};
void outputLine(const StudentsData&);
int main()
{
ofstream outStudent("Students.dat",ios::binary);
if (!outStudent)
{
cerr << "Cannot open file for writing.\n";
exit(EXIT_FAILURE);
}
StudentsData blankStudent = {0, "", 0.0};
for (int i = 0; i < 100; ++i)
{
outStudent.write(reinterpret_cast<const char*>(&blankStudent),
sizeof(StudentsData));
}
int ID;
cout << "Enter student ID (1 to 100, 0 to end input)\n? ";
cin >> ID;
while (ID > 0 && ID <= 100)
{
StudentsData Students;
string studentName;
size_t length;
Students.StudentID = ID;
cout << "Enter Student name (max 19 chars): ";
cin >> studentName;
length = studentName.size();
length = (length < 20 ? length : 19);
studentName.copy(Students.StudentName, length);
Students.StudentName[length] = '\0';
cout << "Enter GPA: ";
cin >> Students.GPA;
outStudent.seekp((Students.StudentID - 1) * sizeof(StudentsData));
outStudent.write(reinterpret_cast<char*>(&Students), sizeof(StudentsData));
cout << "Enter Student ID\n? ";
cin >> ID;
}
outStudent.close();
ifstream inStudent("Students.dat", ios::binary);
if (!inStudent)
{
cerr << "Cannot open file for reading.\n";
exit(EXIT_FAILURE);
}
StudentsData student;
cout << "\n--- Records in File ---\n";
cout << left << setw(10) << "StudentID"
<< setw(16) << "Student Name"
<< setw(10) << right << "GPA\n";
inStudent.read(reinterpret_cast<char*>(&student), sizeof(StudentsData));
while (inStudent)
{
if (student.StudentID != 0)
{
outputLine(student);
}
inStudent.read(reinterpret_cast<char*>(&student), sizeof(StudentsData));
}
inStudent.close();
return 0;
}
void outputLine(const StudentsData& student)
{
cout << left << setw(10) << student.StudentID
<< setw(16) << student.StudentName
<< setw(10) << setprecision(2) << right << fixed
<< student.GPA << endl;
}
