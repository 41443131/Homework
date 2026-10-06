# 41443131

# 作業一

## 解題說明

這次作業有兩個問題。第一題是 Ackermann 函數，要分別寫出遞迴和非遞迴版本。第二題是計算集合的冪集。

## 解題策略

第一題的遞迴版本直接按照題目給的公式來寫。非遞迴版本使用陣列來模擬堆疊，讓程式不用一直呼叫自己。

第二題把每個元素分成兩種情況：放進子集合，或是不放進子集合。一直處理到所有元素都決定完，再把目前的子集合印出來。

## 程式實作

### 題目一：Ackermann 函數遞迴版本

```cpp
#include <iostream>
using namespace std;

int a(int m, int n)
{
    if (m == 0)
    {
        return n + 1;
    }
    else if (n == 0)
    {
        return a(m - 1, 1);
    }
    else
    {
        return a(m - 1, a(m, n - 1));
    }
}

int main()
{
    int m = 0, n = 0;
    cin >> m >> n;
    cout << a(m, n) << endl;
    return 0;
}
```

### 題目一：Ackermann 函數非遞迴版本

```cpp
#include <iostream>
using namespace std;

int a(int m, int n)
{
    int x[10000];
    int top = -1;

    x[++top] = m;

    while (top >= 0)
    {
        m = x[top--];

        if (m == 0)
        {
            n += 1;
        }
        else if (n == 0)
        {
            n = 1;
            x[++top] = m - 1;
        }
        else
        {
            n -= 1;
            x[++top] = m - 1;
            x[++top] = m;
        }
    }

    return n;
}

int main()
{
    int m = 0, n = 0;
    cin >> m >> n;
    cout << a(m, n) << endl;
    return 0;
}
```

### 題目二：冪集

```cpp
#include <iostream>
using namespace std;

void powerset(char s[], int n, char answer[], int k)
{
    if (n == 0)
    {
        if (k == 0)
        {
            cout << "{}";
        }
        else
        {
            cout << "{";

            for (int i = 0; i < k; i++)
            {
                cout << answer[i];

                if (i < k - 1)
                {
                    cout << ", ";
                }
            }

            cout << "}";
        }

        cout << endl;
        return;
    }

    // 不選目前的元素
    powerset(s, n - 1, answer, k);

    // 選目前的元素
    answer[k] = s[n - 1];
    powerset(s, n - 1, answer, k + 1);
}

int main()
{
    char s[3] = {'a', 'b', 'c'};
    int n = 3;
    char answer[3];
    int k = 0;

    powerset(s, n, answer, k);

    return 0;
}
```

## 效能分析

### Ackermann 函數

Ackermann 函數的成長很快，輸入變大時會需要很多次計算。遞迴版本會使用系統的函式堆疊，所以輸入太大可能會 Stack overflow。

非遞迴版本使用 `x[10000]` 來保存資料，因此不使用函式遞迴堆疊。但是陣列大小有限，輸入太大時仍然可能不夠用。

### 冪集

每個元素都有選和不選兩種情況，所以有 `2^n` 個子集合。這個程式的時間複雜度大約是 `O(2^n)`，遞迴深度是 `n`。

## 測試與驗證

### Ackermann 函數測試

| 輸入 | 輸出 |
|---|---:|
| `0 0` | `1` |
| `0 5` | `6` |
| `1 2` | `4` |
| `2 3` | `9` |
| `3 3` | `61` |
| `4 0` | `13` |

遞迴版本和非遞迴版本的輸出相同。

### 冪集測試

輸入集合為 `{a, b, c}`，輸出共有 8 個：

```text
{}
{a}
{b}
{b, a}
{c}
{c, a}
{c, b}
{c, b, a}
```

因為：

```text
2^3 = 8
```

所以輸出數量正確。集合裡面的順序不同不會影響結果。

### 編譯與執行指令

三個程式要分開編譯，因為每個程式都有自己的 `main()`。

```shell
g++ -std=c++17 -o recursive recursive.cpp
g++ -std=c++17 -o nonrecursive nonrecursive.cpp
g++ -std=c++17 -o powerset powerset.cpp
```

執行方式：

```shell
./recursive
./nonrecursive
./powerset
```

## 結論

第一題的遞迴版本可以按照公式計算 Ackermann 函數。非遞迴版本使用陣列模擬堆疊，也可以得到相同結果。

第二題使用遞迴處理每個元素的兩種選擇，成功列出集合的所有子集合。

## 申論及開發報告

### 選擇遞迴的原因

Ackermann 函數本來就是用遞迴定義的，所以使用遞迴寫起來比較直接。冪集的每個元素都有選或不選兩種可能，也適合用遞迴處理。

### 遇到的問題

一開始測試 Ackermann 函數時，如果輸入太大，程式會執行很久，甚至出現 Stack overflow。後來在非遞迴版本中使用陣列來模擬堆疊。

寫冪集時要注意 `k` 的值，因為 `k` 是目前子集合裡面的元素數量。如果選取元素，就要把 `k` 加 1。

### 心得

這次作業讓我比較了解遞迴的寫法，也知道遞迴可以用陣列和迴圈來模擬。冪集的部分則是利用每個元素選或不選，找出所有可能的組合。
