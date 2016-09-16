# The Schwimmbad
[![Build status](http://img.shields.io/travis/adrn/schwimmbad/master.svg?style=flat)](http://travis-ci.org/adrn/schwimmbad)
[![License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](https://github.com/adrn/schwimmbad/blob/master/LICENSE)

A common interface to parallel processing pools.

Enables easy switching between serial execution, `multiprocessing`, and MPI
by providing a common interface.

## Example

If you can write your code following the [MapReduce](https://en.wikipedia.org/wiki/MapReduce)
programming model, you can trivially parallelize it using _The Schwimmbad_. For example, if you can
structure your code so that a 'worker' function operates on elements from a list of tasks, like
this:

```python
def worker(task):
    # do something with the task!
    return task**2

def main():
    tasks = range(100)
    results = map(worker, tasks)

if __name__ == "__main__":
    main()
```

with a few added lines of code, you can make it runnable with `multiprocessing` and MPI at the
switch of a command-line flag:

```python
import schwimmbad

def worker(task):
    # do something with the task!
    return task**2

def main(pool):
    tasks = range(100)
    results = pool.map(worker, tasks)
    pool.close()

if __name__ == "__main__":
    from argparse import ArgumentParser
    parser = ArgumentParser(description="")

    group = parser.add_mutually_exclusive_group()
    group.add_argument("--ncores", dest="n_cores", default=1,
                       type=int, help="Number of processes (uses multiprocessing).")
    group.add_argument("--mpi", dest="mpi", default=False,
                       action="store_true", help="Run with MPI.")
    args = parser.parse_args()

    pool = schwimmbad.choose_pool(mpi=args.mpi, processes=args.n_cores)
    main(pool)
```

## Installation

The project is installable with

```bash
python setup.py install
```

## Dependencies

* [six](https://pythonhosted.org/six/)
* [mpi4py](https://mpi4py.readthedocs.io) (optional - needed for MPI pool support)
