# 41443131
# 作業二
**Polynomial 多項式運算**
## 解題說明
這次作業是完成 Polynomial 類別，讓程式可以儲存多項式，並且進行加法、乘法和代入計算。
我用 Term 表示每一項資料，裡面有係數和指數，再用動態陣列把每一項存起來。輸入時先輸入項目數量，再輸入係數和指數。項目的指數要由小到大輸入。
加法是比較兩個多項式目前的指數，指數相同就把係數相加。乘法則是把每一項互相相乘，再把相同指數的項目加起來。主程式用選單讓使用者選擇功能。
## 程式實作
以下是完整程式碼。
```cpp
#include<iostream>
#include<cmath> // 用來算 pow 的
using namespace std;
class Polynomial;
class Term 
{
	friend Polynomial;
	friend ostream& operator<<(ostream& out, const Polynomial& a);
private:
	float coef; // 係數
	int exp;    // 次方
};
class Polynomial
{
private:
	Term* termArray; // 動態陣列放 Term
	int capacity;    // 陣列容量
	int terms;       // 目前有幾項
public:
	// 預設建構子：容量設 2，項數 0
	Polynomial() :capacity(2), terms(0) 
	{
		termArray = new Term[capacity];
	}
	Polynomial(const Polynomial& a); // 複製建構子 (深 copy)
	~Polynomial();                   // 解構子 (釋放記憶體)
	Polynomial& operator=(const Polynomial& a); // 指定運算子多載
	void newTerm(const float theCoef, const int theExp); // 新增一項
	Polynomial Add(Polynomial b);  // 多項式相加
	Polynomial Mult(Polynomial b); // 多項式相乘
	float Eval(float x);           // 代入 x 計算數值
	friend istream& operator>>(istream& in, Polynomial& a); // 輸入
	friend ostream& operator<<(ostream& out, const Polynomial& a); // 輸出
};
// 複製建構子
Polynomial::Polynomial(const Polynomial& a)
	: capacity(a.capacity), terms(a.terms) 
{
	termArray = new Term[capacity];
	copy(a.termArray, a.termArray + terms, termArray);
}
// 解構子
Polynomial::~Polynomial() 
{
	delete[] termArray;
}
// 指定運算子 (=)
Polynomial& Polynomial::operator=(const Polynomial& a) 
{
	if (this != &a) 
	{
		delete[] termArray;
		capacity = a.capacity;
		terms = a.terms;
		termArray = new Term[capacity];
		copy(a.termArray, a.termArray + terms, termArray);
	}
	return *this;
}
// 新增一項，滿了就自動把空間變兩倍
void Polynomial::newTerm(const float theCoef, const int theExp)
{
	if (theCoef == 0) return; // 係數是 0 就不用存了
	if (terms == capacity)
	{
		capacity *= 2;
		Term* temp = new Term[capacity];
		copy(termArray, termArray + terms, temp);
		delete[] termArray;
		termArray = temp;
	}
	termArray[terms].coef = theCoef;
	termArray[terms++].exp = theExp;
}
// 多項式相加
Polynomial Polynomial::Add(Polynomial b) 
{
	Polynomial c;
	int aPos = 0, bPos = 0; // 用兩個指標去掃 A 跟 B
	// 兩邊都還沒掃完時繼續比次方大小
	while ((aPos < terms) && (bPos < b.terms))
	{
		if (termArray[aPos].exp == b.termArray[bPos].exp) 
		{
			float t = termArray[aPos].coef + b.termArray[bPos].coef; // 次方一樣，係數相加
			if (t) c.newTerm(t, termArray[aPos].exp); // 不為 0 才存進去
			aPos++; bPos++;
		}
		else if (termArray[aPos].exp < b.termArray[bPos].exp) 
		{
			c.newTerm(termArray[aPos].coef, termArray[aPos].exp); // A 比較小，先放 A
			aPos++;
		}
		else 
		{
			c.newTerm(b.termArray[bPos].coef, b.termArray[bPos].exp); // B 比較小，放 B
			bPos++;
		}
	}
	// 把 A 或 B 剩下的直接補進去
	for (; aPos < terms; aPos++)
		c.newTerm(termArray[aPos].coef, termArray[aPos].exp);
	for (; bPos < b.terms; bPos++)
		c.newTerm(b.termArray[bPos].coef, b.termArray[bPos].exp);
	return c;
}
// 多項式相乘
Polynomial Polynomial::Mult(Polynomial b)
{
	Polynomial c;
	for (int i = 0; i < terms; i++) 
	{
		Polynomial temp; // 存 A 的這一項去乘 B 整條的暫時結果
		for (int j = 0; j < b.terms; j++) 
		{
			float newCoef = termArray[i].coef * b.termArray[j].coef; // 係數相乘
			int newExp = termArray[i].exp + b.termArray[j].exp;       // 次方相加
			temp.newTerm(newCoef, newExp);
		}
		c = c.Add(temp); // 每次乘完的結果跟總和相加
	}
	return c;
}
// 代入 x 計算數值
float Polynomial::Eval(float x)
{
	float result = 0;
	for (int i = 0; i < terms; i++) 
	{
		result += termArray[i].coef * pow(x, termArray[i].exp); // 每一項算出來累加
	}
	return result;
}
// 輸入多項式 (in 代表輸入串流，例如 cin)
istream& operator>>(istream& in, Polynomial& a)
{
	int n;
	in >> n; // 先讀入有幾項
	if (!in) return in;
	delete[] a.termArray; // 清空舊的
	a.capacity = 2;
	a.terms = 0;
	a.termArray = new Term[a.capacity];
	for (int i = 0; i < n; i++)
	{
		float coef;
		int exp;
		in >> coef >> exp;
		a.newTerm(coef, exp);
	}
	return in;
}
// 輸出多項式
ostream& operator<<(ostream& out, const Polynomial& a)
{
	if (a.terms == 0)
	{
		out << "0";
		return out;
	}
	for (int i = 0; i < a.terms; i++) 
	{
		if (i > 0 && a.termArray[i].coef > 0)
			out << "+"; // 正數前面補個 + 號好看
		out << a.termArray[i].coef;
		if (a.termArray[i].exp != 0)
			out << "x^" << a.termArray[i].exp; // 次方不是 0 才印 x^exp
	}
	return out;
}
int main()
{
	Polynomial a, b;
	float x;
	int c;
	cout << "A: ";
	cin >> a;
	cout << "B: ";
	cin >> b;
	// 選單迴圈
	while (true)
	{
		cout << "\n========== 選單 ==========" << endl;
		cout << "1. 計算 A + B" << endl;
		cout << "2. 計算 A * B" << endl;
		cout << "3. 計算 A(x)" << endl;
		cout << "4. 計算 B(x)" << endl;
		cout << "5. 重新輸入 A、B" << endl;
		cout << "0. 結束程式" << endl;
		cout << "請選擇功能: ";
		cin >> c;
		if (c == 0) {
			cout << "程式結束。" << endl;
			break;
		}
		switch (c)
		{
		case 1:
			cout << "A(x) + B(x) = " << a.Add(b) << endl;
			break;
		case 2:
			cout << "A(x) * B(x) = " << a.Mult(b) << endl;
			break;
		case 3:
			cout << "輸入x: ";
			cin >> x;
			cout << "A(" << x << ") = " << a.Eval(x) << endl;
			break;
		case 4:
			cout << "輸入x: ";
			cin >> x;
			cout << "B(" << x << ") = " << b.Eval(x) << endl;
			break;
		case 5:
			cout << "輸入A" << endl;
			cin >> a;
			cout << "輸入B" << endl;
			cin >> b;
			break;
		default:
			cout << "重新輸入" << endl;
			break;
		}
	}
	return 0;
}
```
## 效能分析
假設 A 有 n 項，B 有 m 項。
加法會走訪兩個多項式，所以時間大約是 O(n+m)。代入計算會走訪所有項目，所以時間是 O(n)。
乘法需要把 A 和 B 的項目互相相乘，基本上有 n 乘 m 次運算。因為程式還會一直合併結果，所以最壞情況約為 O(n^2m)。
使用的空間主要是儲存多項式的動態陣列。乘法的結果最多有 n 乘 m 項，所以空間約為 O(nm)。
## 測試與驗證
輸入 A：
3
3 0
2 1
1 2
輸入 B：
2
4 0
5 1
選擇1後輸出：
A(x) + B(x) = 7+7x^1+1x^2
選擇2後輸出：
A(x) * B(x) = 12+23x^1+14x^2+5x^3
輸入 x 等於 2 時，A 的結果是 11，B 的結果是 14。選擇 5 可以重新輸入多項式，選擇 0 可以結束程式。測試結果和自己計算的結果相同。
## 申論及開發報告
這次使用動態陣列來存放多項式，因為一開始不知道會有幾個項目。容量不夠時，就增加陣列大小。
程式中也加入複製建構函式、解構函式和指派運算子，避免使用動態記憶體時發生錯誤。
實作時有遇到函式名稱和變數寫錯的問題，修正後再加入選單，讓程式可以正常使用。
