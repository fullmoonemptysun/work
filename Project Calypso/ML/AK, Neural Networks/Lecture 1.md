1. Loss function: a **loss function** or **cost function** (sometimes also called an error function)[[1]](https://en.wikipedia.org/wiki/Loss_function#cite_note-ttf2001-1) is a function that maps an [event](https://en.wikipedia.org/wiki/Event_\(probability_theory\) "Event (probability theory)") or values of one or more variables onto a [real number](https://en.wikipedia.org/wiki/Real_number "Real number") intuitively representing some "cost" associated with the event. An [optimization problem](https://en.wikipedia.org/wiki/Optimization_problem "Optimization problem") seeks to minimize a loss function.


2. Gradient: Vector of partial derivatives of a loss function with respect to the input variables of the model. 

3. Basically, we have a model and we have parameters to it (Weights). Gradient is how the loss function changes as we change each of these weights. We use Autograd that uses backpropagation to calculate these gradients to find the combination of weights that minimizes the gradient.


4. Calculus basics: What does a derivative imply?: Power of the slope (rate of change) at any given point. If we increase the value by a very small point, what is the power of the function? (how much does it change)



# Neural Networks
- Will need big data structures to hold big mathematical expressions (that's what neural networks are).

- After we are done creating the trace of the expressions (scalar valued). We start to attempt backpropagation from the root and calculate the gradients or the derivative of this final loss function (L)/the output w.r.t each parameter through the expression journey.

- We will be interested in some of these parameters that will later become the weights (they will affect the output).  The remaining parameters will be the data for the loss function (which is fixed).
