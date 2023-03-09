# Coroutines
#python 

- Using `yield` more generally than [[Generators|generators]] gives coroutines. `yeild` as an expression allows generator functions to consume values sent to them. However, despite similarities they are two different concepts: generators produce values whereas coroutines mostly consume them. Generators **pull** data through the pipe with iteration. Coroutines **push** data into the pipeline with `send()`.
- Coroutine priming: It is necessary to prime coroutines by first calling `.next()`  or `send(None)`. Coroutines can only receive values at the location of the yield expression. Priming brings the execution to the location of the first yield expression. Decorators can be used to prime coroutines: 
```python
def coroutine(gen_func):
	def prime_coro(*args,**kwargs):
		coro = gen_func(*args, **kwargs)
		coro.next()
		return coro
	return prime_coro

@coroutine
def gen_func()
	...
```

- To prevent coroutines from running indefinitely, they must be closed using `.close()`. `close()` can also be caught in the definition of the coroutine. In fact, this `GeneratorExit` exception cannot be ignored.
- Exceptions can be thrown inside a coroutine using `.throw()`. Exception originates at the yield expression and can be handled in the usual ways.

## Pipelines
- Coroutines can be used to setup processing pipelines.
![[Pasted image 20230302124604.png]]
-  The pipeline needs an initial source which is usually not a coroutine to produce the data, and a sink to serve as the end point.
```python
def source(target):
	while not done:
		item = produce_item()
		...
		target.send(item)
		...
	target.close()

@coroutine
def sink():
	try:
		while True:
			item = (yield)
			...
	except GeneratorExit:
		...
```

- Data can be broadcast to multiple destinations. In fact, coroutines provide power data routing techniques. Simple data processing components can be glued together into more complex data pipelines.

## Coroutines vs. Objects
- Coroutines provide conceptual simplicity: A single function definition is required compared to the object oriented approach with a class definition, multiple method definitions, and some library imports.
- Coroutines are faster because they are free of `self` lookups. 

## Implementation vs. Environment
- With coroutines, the implementation of a task can be separated from its execution environment:
	- Coroutines are the implementation
	- The environment is where the coroutine is executed--threads, subprocesses, network, etc.

## Cautions and Limitations
- A huge number of coroutines may make an application unmanageable.
- The `send()` method must be properly synchronized. Calling it on an already-executing coroutine will crash the program.
- Coroutines cannot be used to create loops or cycles in the pipeline.

## Keeping it Straight
- There are three main uses of `yield`
	- Iteration (like generator functions that produce data)
	- Receiving information (like most coroutines)
	- A trap (for cooperative multitasking) [1]
- Do NOT write functions that do more than one of these at once. Mixing paradigms like this will cause issues.