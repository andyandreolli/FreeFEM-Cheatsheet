# FreeFEM++ Cheatsheet


## Data types: numbers
```C++
dataType variable_name = expression;
```
You can also initialise variables without assigning them a value; multiple initialisations are allowed.
- __int__ > integer number
- __real__ > real number
- __complex__ > complex number
  - *complex a = 12 + 3i*;


## Strings
```C++
string s = "This is a string";
```


## Global variables
- __P__ > returns current point, eg P.x and P.y are coordinates
- __x, y, z__ > return coordinates of current point
- __label__ > returns the label of the boundary the selected point belongs to, 0 if doesn't belong to any
- __region__ > returns region no of current point
- __N__ > returns outwards facing versor for current point if on a curve defined by border


## Arithmetics
- __+, -, *, /, ^__ > classic operators
- __%__ > yields remainder
  - notice that if operands are real, this command calculates the remainder of the integer parts
  - cannot be used with complex numbers
- __conj(no.), real(no.), imag(no.)__ > returns conjugate 
- __max(a,b), min(a,b)__
  - cannot be used with complex numbers
- __a ? b : c__ > is equal to b if a is true, otherwise it's equal to c (arithmetical if)


---


## Arrays
```C++
real[int] = array_name(size);
//alternatively, like in MATLAB; size is calculated automatically
real[int] = array_name(1:3:10);
```
Size can be left unallocated; multiple statements are allowed.
```C++
array_name.resize(newsize);
```


## Matrices
```C++
matrix M = [[0,1,0],
            [1,0,0],
            [0,0,1]];
```
- to change solver: *set(M,solver=sparsesolver);*
  - available: LU, CG, Crout, Cholesky, GMRES, UMFPACK
  - default: GMRES
- transposition: __'__
- term by term operations: __.*, ./__
- solve linear system: *b = A^-1 x*
  - notice: A^-1 is an operator, basically


---


## Loops
```C++
for (int i=1; i<=10; i++)
  {run code}
```
```C++
while (i<=10)
 {run code}
```
__continue__ statement will bypass the code from continue to the end of the loop; __break__ ends the loop.


___


## Built in functions
- pow(x,y), exp(x,y), log(x) [natural], log10(x) [base 10]
- sin(x), cos(x), tan(x), asin(x), acos(x), atan(x)
- sinh(x), cosh(x), tanh(x), asinh(x), acosh(x), atanh(x)
- __floor(x)__ > returns largest integer not greater than x
- __ceil(x)__ > returns smallest integer not less than x
- __rint(x)__ > returns closer integer


## Analyitical functions
```C++
func f = cos(x) + sin(x);
```
Remark: function type is given by expression type


___


## Mesh generation
```C++
border gamma(t=a,b) {x=f(t); y=g(t); label=1};
```
This generates a border that is oriented from a to b, domain must be on the left, so on the right you have the outwards facing versor. Then border is discretised:
```C++
gamma(m); //negative values for reversed orientation
```
To build mesh, borders must intersect on extremes only and result must be a closed curve.
```C++
mesh Th1 = buildmesh(gamma1(10)+gamma2(5));
```


## FE  Space
```C++
fespace Xh(Th,P1);
Xh uh,vh; //initialises FE functions
Xh fef = x^2 + y^2; //automatically interpolated
```
To calculate integrals on the defined space:
```C++
int2d(mesh)(expression)//integration on the mesh
int1d(mesh, label) //integration on boundary "label"
on(label,label,...,uh=0) //boundary condition
```
To calculate derivatives of FE functions - __DOESN'T WORK WITH ANALYTICAL ONES__:
```C++
dx(fef);
```
To access only the degrees of freedom of a FE function, use _function_name[]_ (returns FE function as a vector).


## Solve with problem
```C++
problem laplace(uh,vh) = int2d(mesh)(integrand)
                         + on(label,condition);
                         //implicitly: integral = 0
laplace; //solution of the equation
```
You can set the solver and the preconditioner:
```C++
problem laplace(uh,vh,solver=CG,precon=P) = ...;
```


## Solve with varf
```C++
varf B(u,v) = int2d(Th)(integrand)
              + on(label, condition); //variational form
//define fespace, functions
matrix K = B(Xh,Xh); //stiffness matrix
g[] = -B(0,Xh); //known term
uh[] = K^-1 * g; //solution of linear system
```


---


## Plotting
```C++
plot(thing_to_plot, value=1, fill=1);
```
You might want to pass argument _nbiso=40_ as well. Don't really know what it does.


## Input/output
Syntax is the same as C++: uses __cin__, __cout__, __endl__, __<<__, __>>__.
To print some text on the console:
```C++
cout << "Text that is printed" <<endl;
```
To gather some input from the console:
```C++
cin >> i;
```
To read and write on files, first define their path:
```C++
ofstream of_name("filename.extension"); //for simple output
ofstream of_name("filename.extension",append); //to append
ifstream in_name("filename.extension");
```
Then, usage is the same as __cin__ and __cout__:
```C++
of_name << variable <<"string" <<endl;
if_name >> i;
```


---


## Best practices
- don't use ^ or pow() for powers of 10; use *e* instead
