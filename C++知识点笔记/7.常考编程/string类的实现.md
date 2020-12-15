```c++
#include <iostream>
#include <cstring>

using namespace std;

class my_string {
public:
    my_string(const char *data = nullptr) {
        cout << "默认构造" << endl;
        if (data == nullptr) {
            _data = new char[1];
            _data[0] = '\0';
            _size = 0;
        } else {
            _size = strlen(data);
            _data = new char[_size + 1];
            strcpy(_data, data);
        }
    }

    my_string(const my_string &others) {
        cout << "拷贝构造" << endl;
        _size = others._size;
        _data = new char[_size + 1];
        strcpy(_data, others._data);
    }

    my_string(my_string &&others) {
        cout << "移动构造" << endl;
        _data = others._data;
        others._data = nullptr;
    }

    my_string &operator=(my_string rhs) {
        cout << "拷贝赋值" << endl;
        rhs.swap(*this);//等价于 swap(rhs);
        return *this;
    }

    my_string operator+(const my_string &rhs) {
        cout << "重载operator+" << endl;
        my_string newstr;
        newstr._size = _size + rhs._size;
        newstr._data = new char[newstr._size + 1];
        strcpy(newstr._data, _data);
        strcat(newstr._data, rhs._data);
        return newstr;
    }

    my_string &operator+=(my_string rhs) {
        cout << "重载operator+=" << endl;
        //调用operator+ 返回一个临时对象
        (*this + rhs).swap(*this);
        return *this;
    }

    char &operator[](int n) {
        cout << "下标访问" << endl;
        if (n >= _size) throw std::exception();
        else return _data[n];
    }

    bool operator==(const my_string &others) {
        cout << "重载==" << endl;
        if (others._size != _size) return false;
        return strcmp(_data, others._data) == 0 ? true : false;
    }

    void swap(my_string &rhs) {
        using std::swap;
        swap(rhs._data, _data);
    }

    int size() const { return _size; }

    friend ostream& operator<<(ostream &os, const my_string &str);

    ~my_string() {
        cout << "析构" << endl;
        if (_data) {
            delete[] _data;
            _data = nullptr;
        }
    }

private:
    char *_data;
    int _size;
};

ostream& operator <<(ostream &os, const my_string &str){
    os<<str._data;
    return os;
}
int main() {
    my_string s1("aaa");
    my_string s2 = "bbb";
    my_string s3 = s2;
    my_string s4 = std::move(s1);
    s2 += s4;
    cout<<s2<<endl;
    return 0;
}
```