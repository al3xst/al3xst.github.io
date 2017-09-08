# C/C++ to Python 3 Module

In this post I want to show, how you can create a Python module from your C/C++ program.

What you need:
- gcc/g++
- python 3 (I wont cover python2)
- python 3 setuptools (TODO: look up what exactly is needed)
- python.h (TODO: look up what exactly is needed and how to obtain it)

## Modifying your main.cpp

First of all, we need to include Python.h, just add this line this to your includes: `#include <Python.h>`

Now we need to describe to Python our module:
```c++
static struct PyModuleDef <my_module_definition> = {
  PyModuleDef_HEAD_INIT,                //This is always required
  "your_module_name",                   //char* m_name; in python you will call "import your_module_name"
  "Docstring describing your module!",  //char* m_doc;
  -1,                                   //Py_ssize_t m_size; -1 means no support for sub_interpreters, 
                                        //see Docu for more information on this flag: 
                                        //https://docs.python.org/3/c-api/module.html#c.PyModuleDef
  my_module_methods                     //A pointer to the PyMethodDef struct
};
```

Next we have to describe all available methods we want to be able to call from Python:
```c++
static struct PyMethodDef my_module_methods[] = {
  {
    "methode_name",                     //char* m_name; This is the name of our method, 
                                        //in Python you will call "method_name()° to execute the function behind ml_meth
    my_c_method,                        //PyCFcuntion ml_meth; This is a pointer to the c function
    METH_VARARGS,                       //int ml_flags; Here you can tell python if we want args for our function
                                        //see more at: https://docs.python.org/3.6/c-api/structures.html#c.PyMethodDef
    "Docstring describing your method!" //char *m_doc;
  },
  {nullptr, nullptr, 0, nullptr}        //sentinel
};
```

Now we need to implement the method we defined in the PyMethodDef struct. 
In this case, its the method `my_c_method`. Unfortunatly we can't directly use our C/C++ function,
because we have to prepare the arguments we get from Python and later on prepare the result to pass back to Python.
```c++
static PyObject* my_c_method(PyObject* self, PyObject* args) {
  //lets say in our example our method takes 5 arguments, all double values
  double a, b, c, d, e; //storage for the input arguments
  
  PyArg_Parse_Tuple(args, "ddddd", &a, &b, &c, &d, &e);
  //
}
}```
