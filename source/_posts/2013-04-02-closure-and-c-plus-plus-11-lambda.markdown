---
layout: post
title: "closure and c++11 lambda"
date: 2013-04-02 22:51
comments: true
keywords: C++, C++11, C++0x, closure, 闭包, lambda 
categories: [C++, C++11, learning cycle]
---

##What is "Closure"
In computer science, a closure (also lexical closure or function closure) is a function or reference to a function together with a referencing environment—a table storing a reference to each of the non-local variables (also called free variables) of that function. A closure unlike a plain function pointer allows a function to access those non-local variables even when invoked outside of its immediate lexical scope.
<!-- more -->

an example in python 3:
{% codeblock lang:python %}
def counter():
    x = 0
    def increment(y):
        nonlocal x
        x += y
        print(x)
    return increment
{% endcodeblock %}
        
The closure returned by counter can be assigned to a variable:
{% codeblock lang:python %}
counter1_increment = counter()
counter2_increment = counter()
{% endcodeblock %}
    
Invoking increment through the closures will give the following results:
{% codeblock lang:python %}
counter1_increment(1)    # prints 1
counter1_increment(7)    # prints 8
counter2_increment(1)    # prints 1
counter1_increment(1)    # prints 9
{% endcodeblock %}

The closure and anonymous function are distinct concepts. A closure retains a reference to the environment at the time it was created (for example, to the current value of a local variable in the enclosing scope) while a generic anonymous function need not do this.
    
## lambda of C++11
C++11 with the lambda expression partially support closure -- the programmer chooses whether the lambda makes copies of the captured stack variables or only references them. References will cause UndefinedBehavior if the lambda escapes the scope it was created in.  That is because C++ run-time memory model allocates all local variables on a linear stack. A function's local variables are deallocated when the function returns. However, a closure requires that the free variables it references survive the enclosing function's execution. So typically, languages that natively support closures also use garbage collection.

###basic structure of c++11 lambda expression:
    
    [capture](parameters)->return_type {body}

###variable capture

    [] Capture nothing (or, a scorched earth strategy?)
    [&]	Capture any referenced variable by reference
    [=]	Capture any referenced variable by making a copy
    [x, &y] Capture x by making a copy, but capture y by reference
    [=, &foo] Capture any referenced variable by making a copy, but capture variable foo by reference
    [bar] Capture bar by making a copy; don't copy anything else
    [this] Capture the this pointer of the enclosing class



###Storing Closures
Closure types not specified, but two easy ways to store closures:
####auto :
{% codeblock lang:cpp %}
auto multipleOf5 = [](long x) { return x % 5 == 0; };
std::vector<long> vl; 
…
vl.erase(std::remove_if(vl.begin(), vl.end(), multipleOf5), vl.end());
{% endcodeblock %}

####std::function:
{% codeblock lang:cpp %}
std::function<bool(long)> multipleOf5 =  // see next page for syn
[](long x) { return x % 5 == 0; };
…
vl.erase(std::remove_if(vl.begin(), vl.end(), multipleOf5), vl.end());
{% endcodeblock %}

##Typical Usages

#### 1. Facilitate STL algorithm

{% codeblock lang:cpp %}
std::vector<int> v; 
… 
auto it = std::find_if(v.cbegin(), v.cend(), 
                       [](int i) { return i > 0 && i < 10; } );

{% endcodeblock %}

{% codeblock lang:cpp %}
int main()
{
  char s[]="Hello World!";
  int Uppercase = 0; //modified by the lambda
  for_each(s, s+sizeof(s), [&Uppercase] (char c) {
    if (isupper(c)) {
      Uppercase++;
    }
  });
  cout<< Uppercase<<" uppercase letters in: "<< s<<endl;
}
{% endcodeblock %}

#### 2. Making Delegates with Lambdas

{% codeblock lang:cpp %}
class DeliveryHistoryService {
public:
  DeliveryHistoryService(std::function<TreatmentProgress(std::string)> finder);

  ...
};

class TpiTreatmentService {
  ...

    TreatmentProgress treatmentProgress(std::string treatementId);
};

.....
TpiTreatmentService treatmentService;
DeliveryHitoryService([&](std::string id){return treatmentService.treatmentProgress(id)});
{% endcodeblock %}

#### 3. Simplify the unit test

{% codeblock lang:cpp %}
void testDelieveryHistoryService() {
  . . .    
    TreatmentProgress tmpTreatmentProgress;
  DeliveryHistoryService([&](std::string){return tmpTreatmentProgress});
}
{% endcodeblock %}


#### 4. Simplify RAII
[RAII](http://en.wikipedia.org/wiki/Resource_acquisition_is_initialization) stands for Resource Acquisition Is Initialization,  was invented by Bjarne Stroustrup[2] to deal with resource allocation and deallocation in C++. The idea is that you want to acquire resources during the initialization of objects, i.e. as soon as possible, so that you cannot accidentally use an uninitialized object, and also that you want your object to automatically release the resource upon destruction. One of the main advantages of this pattern is that your resources will always be released, even if there are errors or exceptions between when your object is initialized and your object goes out of scope.

{% codeblock lang:cpp %}
class FileCloser
{
public:
  FileCloser(char* fname)
  {
    fp = fopen(fname, "r");
  }
  void ReadLine(char* line, int count)
  {
    fgets(line, count, fp);
  }
  ~FileCloser()
  {
    fclose(fp);
  }
private:
  FileCloser() { }
  FILE* fp;
};

int main(int argc, char* argv[])
{
  FileCloser fc("test.txt");
  char line[100];
  fc.ReadLine(line, 100);
  cout << line << endl;
  return 0;
}
{% endcodeblock %}


{% codeblock lang:cpp %}
int main(int argc, char* argv[])
{
  FILE* fp = fopen("test.txt", "r");
  shared_ptr<void> fileReleaser(fp, [](FILE* fp) { fclose(fp); });
  char line[100];
  fgets(line, 100, fp);
  cout << line << endl;
  throw;
  return 0;
}
{% endcodeblock %}

{% codeblock lang:cpp %}
class ScopeGuard
{
public:
  explicit ScopeGuard(std::function<void()> onExitScope)
    : onExitScope_(onExitScope), dismissed_(false)
  { }

  ~ScopeGuard()
  {
    if(!dismissed_) {
      onExitScope_();
    }
  }

  void Dismiss() {
    dismissed_ = true;
  }

private:
  std::function<void()> onExitScope_;
  bool dismissed_;

private: // noncopyable
  ScopeGuard(ScopeGuard const&);
  ScopeGuard& operator=(ScopeGuard const&);
};
{% endcodeblock %}

{% codeblock lang:cpp %}
HANDLE h = CreateFile(...);
ScopeGuard onExit([&] { CloseHandle(h); });
{% endcodeblock %}

{% codeblock lang:cpp %}
ScopeGuard onFailureRollback([&] { /* rollback */ });
... // do something that could fail
onFailureRollback.Dismiss();
{% endcodeblock %}



