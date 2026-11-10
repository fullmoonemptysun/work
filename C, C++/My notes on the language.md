
- We cannot do the following:
```cpp
switch(var){
case(val)://{
	int var2 = 332;
	}
	break;
//}
default:
	cout << "NOT POSSIBLE IN C++" << endl;
```
Reason: All cases share the same scope and so default knows the same variable `var2` but if the case skips and jumps to default the variable is never initialized and so this is an undefined behavior prohibited in c++. We must cover the initialization (only) with braces to give it its own scope. But its cleaner to cover the full case block.