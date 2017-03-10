# Singularity container in Jupyter Notebooks

These are notes on how to run singularity containers
inside Jupyter Notebooks.

## Example use case

We provide `tensorflow` on the cluster from a singularity container:

~~~
[atrikut@node1669 ~]$ module add tensorflow/1.0
[atrikut@node1669 ~]$ module list
Currently Loaded Modulefiles:
  1) singularity/2.2.1   2) /tensorflow/1.0
  [atrikut@node1669 ~]$ which python
  alias python='singularity /path/to/ubuntu_tensorflow_v1.0_gpu.img /usr/bin/python'
~~~

The `python` command is now aliased to `singularity exec ... python`;
so that when users run `python`, they are actually running a singularity container under the hood.

~~~
[atrikut@node1669 ~]$ python
Python 2.7.6 (default, Mar 22 2014, 22:59:56)
[GCC 4.8.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow
'/usr/local/lib/python2.7/dist-packages/tensorflow/__init__.pyc'
>>>
~~~

We would like to do the same from Jupyter Notebook, i.e., we'd like to enable
users to create Notebooks which use the container Python.

## The `kernel.json` file

Jupyter Notebook allows you to create notebooks
with various backends for executing code, called "kernels".
Thus you can have, for example a "Python 2" kernel for notebooks containing Python 2 code,
a "Python 3" kernel for notebooks containing Python 3 code,
and a "MATLAB" kernel for notebooks containing MATLAB code.

Kernels are defined in `.json` files, which can be located in
`~/.local/share/jupyter/kernels` (among other places).
Here is an example of a `kernel.json` file for Python 2:

~~~
{
 "display_name": "Python 2",
 "language": "python",
 "argv": [
  "/software/anaconda/4.2.0/bin/python",
  "-m",
  "ipykernel",
  "-f",
  "{connection_file}"
 ],
 "display_name": "Python 2 (Anaconda)"
}
~~~

## `kernel.json` for Python in a singularity container

You can similarly define a kernel
that uses Python from a singularity container.
Make sure that you install `ipykernel` inside the container:

~~~
$ pip install ipykernel
~~~

And create a `kernel.json` similar to the following:

~~~
{
 "language": "python",
 "argv": ["/path/to/singularity",
   "exec",
   "/path/to/ubuntu_tensorflow_v1.0_gpu.img",
   "/usr/bin/python",
   "-m",
   "ipykernel",
   "-f",
   "{connection_file}"
 ],
 "display_name": "Python 2 (Tensorflow)"
}
~~~

Now you will see a "Python 2 (Tensorflow)" option
in the list of Jupyter Notebook kernels.
