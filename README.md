## Lecture 1

The main goal of this course is to build a foundational understanding of **computational vision**. However, before we can dive into the material, we need to clear something up right out of the gate: _What exactly is computational vision, and how does it differ from traditional computer vision?_

### Computer Vision

Computer vision is the field where we try to enable machines—like cameras, robots, and self-driving cars—to mimic vision and generate useful outputs based on visual cues. However, these systems aren't actually interested in understanding or replicating _human_ vision. Instead, they care strictly about the mathematical frameworks and engineering tricks that deliver the best, most accurate results for a given task.

### Computational Vision

While computer vision and computational vision share a lot of the same topics and features, **computational vision** is specifically concerned with modeling and understanding how humans _actually_ see.

In an attempt to decode human vision from a biological perspective, scientists have come up with a variety of explanations across different levels of human anatomy:

- **The Cell Level:** Some researchers argue that vision happens because specific, individual cells fire in response to certain visual features.
- **The Circuit Level:** Others look at the brain's circuitry, arguing that complex networks of interconnected neurons map directly to specific objects.
- **The Brain Region Level:** Another group might reason that distinct, specialized areas of the brain are responsible for dedicated tasks across a person's visual system.

While all of these biological perspectives are technically correct, they don't actually explain the _mechanistic_ and _algorithmic_ level of human vision—the actual rules and processes computing the data. Bridging that gap and understanding the algorithm behind the sight is exactly what we aim to do throughout this course.

### Course Material

Throughout this course, we will focus our attention on three core pillars of the human visual system. We will break these down to understand exactly how our brains process the complex sensory data coming through our eyes:

- **1. Motion Processing:** How do we perceive movement, calculate the speed and direction of objects, and navigate through a constantly changing environment?
- **2. Color Processing:** How does our visual system interpret different wavelengths of light, maintain color constancy under changing light conditions, and use color to distinguish objects?
- **3. Shape/Stereo Processing:** How do we perceive depth, construct 3D representations of the world from flat 2D retinal images, and recognize distinct geometric shapes?

### Marr's Three Levels

Now that we have mapped out the scope of our material, we need to define the framework we will use to interact with and understand it. But before we do, consider this famous quote by neuroscientist David Marr:

> "Trying to understand perception by studying only neurons is like trying to understand bird flight by studying only feathers: It just cannot be done. In order to understand bird flight, we have to understand aerodynamics; only then do the structure of feathers and different shapes of birds' wings make sense." — David Marr (1982)

To avoid the trap of looking _only_ at the "feathers" (the biology), Marr established a framework that breaks down any information-processing system into three distinct levels of analysis:

- **1. Computational Level (The "What" and "Why"):** What is the goal of the computation, why is it appropriate, and what is the underlying logic or strategy by which it can be carried out?
- **2. Representation and Algorithm Level (The "How"):** How can this computational theory be implemented? Specifically, what is the representation for the input and output, and what is the step-by-step algorithm used to transform one into the other?
- **3. Implementation Level (The Physical Machinery):** How can the representation and algorithm be physically realized? What is the actual hardware or biological substrate executing the process?

### The Classic Example: A Simple Calculator

To see how these tie together, let’s look at Marr’s original example of a basic cash register or calculator designed for addition:

- **Computational Level:** The goal is to find the sum of two numbers. The logic dictates that addition is commutative (A + B = B + A) and associative.
- **Representation & Algorithm Level:** The numbers are represented as binary strings (0s and 1s), and the system uses a binary addition algorithm (carrying the 1, etc.) to compute the total.
- **Implementation Level:** This is the concrete hardware where the program runs—in this case, silicon circuits, transistors, and logic gates.

### Our Focus in This Course

While all three levels are fascinating, **our primary concern in this course is at the first level: the Computational Level.** Because of this, we will constantly be asking core questions like:

1. What is the fundamental problem that the visual system is trying to solve?
2. Why is this specific problem so difficult?
3. What assumptions can we make about the physical world to make the problem solvable?

### Summary

To recap, we’ve defined one of the main goals of computational vision as seeking to understand _how_ humans see. Throughout this course, we will heavily focus on Marr’s **Computational Level**—investigating why visual problems are difficult and what constraints make them solvable. While computational vision is deeply intertwined with traditional computer vision, it asks fundamentally different, human-centric questions.

### Quiz

**Scenario:** Luke and Han are trying to catch a mosquito. Every time they raise their hand, the mosquito flies away.

- **Luke says:** This is because the mosquito has a specific neuron in its visual cortex that fires every time a hand appears in its visual field.
- **Han says:** The mosquito’s visual system performs a dot product between the incoming image and an internal template of a hand. If that dot product is greater than a specific threshold, the mosquito flies away.

**Question:** At which of Marr’s three levels is each explanation?

### Answer Breakdown

> - **Luke's Explanation is at the Implementation Level.**
>     
>     By pointing directly to a physical, biological structure—the specific neuron firing in the visual cortex—Luke is describing the hardware machinery executing the behavior.
>     
> - **Han's Explanation is at the Representation and Algorithm Level.**
>     
>     Han is describing the step-by-step mathematical strategy. He defines the representation (an incoming image and a template) and the exact algorithm (calculating a dot product and testing if it is greater than a threshold).
>


## Lecture 2: Motion Processing (Part 1)

### Motion Psychophysics

To kick off our deep dive into motion, we need to introduce **psychophysics**. This is the scientific field that attempts to map the relationship between physical stimuli ($\Phi$) and psychological perception ($\Psi$).

Historically, researchers have studied a wide range of physical phenomena to see how a change in the physical world affects our mental perception of that change. For instance, if you gradually turn up the volume on a speaker, people will perceive the sound getting louder. However, a strict 1:1 mapping is almost always incorrect; doubling the physical volume of the speaker does not necessarily mean we perceive it as being exactly twice as loud.

Since this course focuses specifically on computational vision rather than general perception, we need to simplify how we represent our input data. To do this, we will focus primarily on **two-dimensional motion**, leaving out complex factors like rotation and changes in depth. This allows us to mathematically represent any motion vector using just two numbers:

$$(V_x, V_y)$$

Correspondingly, our visual system is capable of perceiving both of these directional components of movement.

### Infinite Lines and the Problem of Ambiguity

When we observe the movement of finite objects in a fully visible space, tracking motion is relatively straightforward. However, as psychologist Hans Wallach discovered in 1935, **infinite lines are fundamentally ambiguous**. This means we don't necessarily perceive their true physical motion because we cannot see the boundaries or endpoints of the line.

Let's break this down using a finite line segment as an example:
![[Pasted image 20260620153230.png]]
This image demonstrates a finite line shifting. If we translate the line toward the positive x-direction, it shifts right. If we shift it down toward the negative y-direction, we get a completely different position (represented by the dashed line). We can easily tell these two movements apart because we can track the moving endpoints of the segment.

Now, consider what happens if that same line becomes **infinite** (or if we view it through a restricted aperture where the ends are hidden):

![[Pasted image 20260620153427.png]]
 Because the line is infinite, we completely lose our reference points. A physical movement directly to the right looks identical to a physical movement directly downward. Without visible endpoints, the two distinct physical actions produce the exact same visual stimulus, making infinite lines inherently ambiguous.
This visual uncertainty introduces a classic challenge in motion processing—one that our brains have to solve using specific assumptions about how the world works.!

### The Mathematics of Motion Ambiguity

To gain a precise mathematical understanding of how these infinite lines move, we can decompose any true velocity vector into two perpendicular components.

Let $v$ be the actual motion vector of the line. We can split $v$ into:

1. **Normal Velocity ($v^{\perp}$):** The component orthogonal (perpendicular) to the orientation of the line.
2. **Parallel Velocity ($v^{\parallel}$):** The component running completely parallel to the line.

Mathematically, this gives us:

$$v = v^{\perp} + v^{\parallel}$$

![[Pasted image 20260620155140.png]]
 - **Black Vector ($v$):** The true physical motion of the line.
 - **Red Vector ($v^{\perp}$):** The normal velocity component.
 - **Blue Vector ($v^{\parallel}$):** The parallel velocity component.    

Based on scientific observations, **the human visual system is completely blind to the parallel velocity ($v^{\parallel}$) of an infinite line.** Because the line lacks distinct features along its length, shifting it parallel to itself changes absolutely nothing in our visual field.

Consequently, the _perceived_ motion of an infinite line is strictly determined by its normal velocity ($v^{\perp}$). This means an infinite number of different physical movements can produce the exact same perceived motion, as long as they all share the identical normal velocity component.

### Wallach’s Formalization (1935)

Hans Wallach formalized this phenomenon through two central observations:

1. **The Indistinguishability Principle:** Let $M$ be a movie generated by an infinite line of orientation $\theta$ moving at velocity $v$. Any other physical velocity $v'$ that shares the exact same normal velocity ($v'^{\perp} = v^{\perp}$) will generate the _exact same movie $M$_.
2. **The Perceptual Claim:** When presented with a movie of an infinite line of orientation $\theta$ moving at velocity $v$, humans will always perceive the **normal velocity** ($v^{\perp}$).

**Why does the brain choose the normal velocity?** Wallach proved that the normal velocity vector is mathematically the **shortest vector** (the slowest velocity) capable of producing that specific visual data.

### The Computational View (Bringing back Marr)

Let’s connect this back to the core questions we established for Marr's first level (The Computational Level):

- **What is the problem?** Estimating the true physical motion of an object in the world.
- **Why is it difficult?** Because of the **Aperture Problem** (the inherent ambiguity of infinite or edge-constrained lines where parallel motion is hidden). The data hitting our eyes is underdetermined—one visual stimulus can mean a million different physical realities.
- **What assumptions make it solvable?** Our visual system solves this puzzle by applying an internal constraint or preference: **Humans possess a strong biological bias toward slower motions.** By assuming the world moves as slowly as possible to explain the data, the brain successfully snaps the ambiguous input into a single, definitive perception (the normal velocity).


### The Barber Pole Effect

Building on the ambiguity of infinite lines, Wallach designed a second set of experiments to observe what happens when lines move through confined windows, or apertures.

> 💡 **Interactive Check:** To get a clear visual handle on this experiment, take a quick look at the interactive [Barber Pole Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/).
![[Pasted image 20260620160831.png]]

Formally, Wallach defined this second phenomenon as follows:

> _For a line at orientation $\theta$ moving through a rectangular aperture of orientation $\theta_A$ and at velocity $v$, the perceived direction of motion is biased toward $\theta_A$._

This happens because the edges of the window slice the line, creating endpoints known as **terminators**.

### Terminator Velocity

To understand why the shape of the window dictates the perceived motion, we can look at the mathematical modeling of **Terminator Velocity**.

By definition: For a line at orientation $\theta$ that intersects a static boundary line of orientation $\theta_2$, the _terminator velocity_ is the vector that tracks the physical movement of that intersection point. This velocity is constrained to move exclusively along the boundary, meaning **this velocity vector is always in the direction of $\theta_2$.**

![[Pasted image 20260620165856.png]]

### Wallach’s Third Phenomenon: Terminator Dominance

If you revisit the simulation, you will notice that the rectangular window creates two different types of boundaries: the long edges and the short edges. This brings us to Wallach’s third major observation regarding how our brain reconciles these competing signals:

> _For a line at orientation $\theta$ moving through an aperture at velocity $v$, let $v^{T_i}$ be the velocity of the individual terminators ($i$). If there is a **dominant** terminator velocity (i.e., for many points $i$, $v^{T_i} = v^*$), then the perceived velocity of the entire line will be $v^*$.

In simpler terms, because the rectangular aperture has much longer vertical edges than horizontal ones, there are significantly more terminators traveling vertically than horizontally. The visual system aggregates these signals, allows the majority ("dominant") terminator velocity to win, and tricks your brain into perceiving a vertical upward motion—exactly like the spinning stripes on a classic barber pole!

## Summary

To wrap up this portion of the lecture:

- **Psychophysics** acts as the bridge mapping physical stimuli to our psychological perception.
    
- The motion of an **infinite line** is inherently ambiguous due to the lack of visible feature points, leading humans to perceive only the **normal velocity** (the slowest physically consistent motion).
    
- When a line is bound by a window, its perceived motion depends heavily on **terminator velocity**—the movement of the intersection points along the edges of the aperture.
    

## Quiz

Look closely at the two lines below:
![[Pasted image 20260620171130.png]]

**Question:** What will be the perceived direction of motion for each of these two lines?

### Answer Breakdown

- **The Left Line (Infinite Vertical Line):** The line is perfectly vertical, which means its **parallel direction is straight down** and its **normal direction is to the right**. Since humans are blind to parallel motion along an infinite line, the downward component of the arrow completely disappears from our perception. Therefore, we perceive the line moving **strictly to the right**.
- **The Right Line (Vertical Line with Horizontal "I-Bar" Caps):** Here, the terminators are formed by the vertical line intersecting the horizontal caps. As the line moves down and to the right, these intersection points are physically constrained to slide along those horizontal boundaries. Because the terminators travel **horizontally to the right** along the cap edges, this dominant terminator velocity dictates our perception. Therefore, we perceive this line moving **strictly to the right** as well!

Here is the polished version of your Lecture 3 notes. I have cleaned up the terminology, formatted the equations and variables using clean Markdown/LaTeX, and structured the explanations of the competing theories so they are crystal clear for studying.

I have also preserved the simulation link and your image placeholders exactly as they were!

## Lecture 3: Combining Motion Signals

### The Constraint Line

To build on the Aperture Problem from the last lecture, let's introduce some formal mathematical terminology to describe that family of ambiguous velocities.

For a line of orientation $\theta$ moving with velocity $v$, the entire set of possible physical velocities that share the exact same normal velocity ($v^{\perp}$) is called the **Constraint Line**.

![[Pasted image 20260620202138.png]]
Admittedly, defining this as a "constraint line" doesn't change anything about what we have discussed so far. However, this geometric tool becomes incredibly powerful the moment we move past a single isolated line and start looking at scenes with **multiple** moving lines.

### Combining Lines: Wallach's Intersection of Constraints (IOC)

What happens when our visual system is confronted with two overlapping, moving gratings or lines?

> 💡 **Interactive Check:** To see this phenomenon in action, take a moment to watch the interactive [Intersection of Constraints Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/VA_IOC.html).

As Wallach observed, when two different sets of lines move over one another, the human visual system doesn't see two separate movements. Instead, the two sets of lines appear to lock together and **move coherently** as a single unified object (at least under certain conditions).

![[Pasted image 20260620204417.png]]

### Interpreting Coherent Motion: Two Competing Theories

To explain how the brain computes the single, unified direction of this combined object, researchers introduced two major competing hypotheses:

#### 1. The Vector Average (VA)

This theory suggests a bottom-up, blending approach. It states that the perceived motion of the combined object is simply the mathematical **vector average** of the individual perceived motions (the normal velocities) of the two separate line sets.
- _In short:_ The brain looks at where Line A is going, looks at where Line B is going, and splits the difference down the middle.

#### 2. The Intersection of Constraints (IOC)

This theory takes a more rigid, geometric approach. It states that the perceived motion is the **exact intersection point** where the constraint lines of the individual motions cross.
- _In short:_ Because each line carries a whole "constraint line" of possible true velocities, the brain plots both constraint lines. The single point where they intersect represents the _only_ true physical velocity that satisfies both moving lines simultaneously.


---
### How to Compute the Constraint Lines and the IOC
To understand how the brain—or a computer vision algorithm—finds the Intersection of Constraints (IOC), we look at the underlying vector mathematics.

#### 1. Defining the Constraint Line Equation

Let a line have a known unit normal vector $\hat{n} = (\cos\theta, \sin\theta)$, where $\theta$ is the perpendicular angle of the line. If the line has a measured normal velocity magnitude of $c$, then its normal velocity vector is:

$$v^{\perp} = c\hat{n}$$

Because we are entirely blind to any parallel movement, the true, absolute velocity vector $v = (V_x, V_y)$ could be _any_ vector whose projection onto the normal vector equals $c$. This gives us the **Motion Constraint Equation**:

$$v \cdot \hat{n} = c$$

Expanding this into standard 2D vector components yields a linear equation for the constraint line:

$$V_x \cos\theta + V_y \sin\theta = c$$

Every point $(V_x, V_y)$ along this straight line in velocity space represents a physically valid velocity consistent with the visual data.

#### 2. Computing the Intersection (The IOC Solution)

When we have two different sets of overlapping lines (Line 1 and Line 2) moving coherently, we get a system of two linear equations with two unknowns ($V_x$ and $V_y$):

$$\begin{cases} V_x \cos\theta_1 + V_y \sin\theta_1 = c_1 \\ V_x \cos\theta_2 + V_y \sin\theta_2 = c_2 \end{cases}$$

Where:

- $\theta_1, \theta_2$ are the normal orientations of the two line configurations.
- $c_1, c_2$ are their respective measured normal velocity magnitudes.

By solving this system using standard linear algebra, we pinpoint the single coordinate $(V_x^*, V_y^*)$ where the two constraint lines cross. This intersection represents the definitive, unambiguous true velocity of the pattern that our visual system snaps onto.

---

Here is the polished version of your notes. I have cleaned up the technical phrasing, formatted the formulas into clear, standalone LaTeX blocks, and fixed a few minor typos (like "lucas granade" and "lucas kasde"). I also found a great foundational paper to link to for the real-world extension, and provided an accurate breakdown for your quiz at the end!

## Motion Perception in Computer Vision

To gain a deeper understanding of human vision, let’s pivot and look at how _computers_ solve the problem of motion.

Formally, the problem is defined as follows: Given two consecutive image frames, $I_1$ and $I_2$, what is the velocity vector $v = (V_x, V_y)$ at which the scene is moving?

![[Pasted image 20260620205559.png]]

At first glance, one might argue this problem is trivial and can be solved simply by finding a displacement such that:

$$I_2(x + V_x, y + V_y) = I_1(x, y)$$

However, this equation relies on a massive underlying assumption that frequently fails in the real world: **Brightness Constancy**. It assumes that a physical point on an object does not change its pixel intensity value as it moves from one frame to the next.

To solve this mathematically, we look for the velocity that minimizes the **Sum of Squared Differences (SSD)**:

$$v^* = \arg\min_v \text{SSD}(V_x, V_y)$$

Where the SSD is defined as:

$$\text{SSD}(V_x, V_y) = \sum_{x,y} \left( I_2(x + V_x, y + V_y) - I_1(x, y) \right)^2$$

Under a perfect assumption of constant brightness, there will be a true solution where the minimum SSD is exactly zero. In real-world conditions, minimizing this equation gives us a highly accurate estimation of the true motion.

A naive, "brute-force" algorithm to solve this would simply test a massive grid of different potential velocities and choose the one that yields the lowest SSD. While computationally inefficient, it is a valid baseline approach.

💡 **The Bridge Between Fields (Assuming Constant Brightness):** It is easy to observe that **under the strict assumption of constant brightness**, the Intersection of Constraints (IOC) from human psychophysics yields an SSD of exactly zero. Therefore, under these ideal conditions, the human IOC velocity is mathematically identical to the computer's optimal $v^*$. This reveals a beautiful convergence between computer vision and computational vision, showing that both fields are optimizing for the exact same physical reality.

### The Lucas-Kanade Algorithm

Because a brute-force search is highly impractical, Bruce Lucas and Takeo Kanade introduced an algorithm in 1981 that approximates the solution efficiently.

If we assume the velocity displacements $V_x$ and $V_y$ are small, we can use a first-order **Taylor series approximation** to linearize our SSD objective function:

$$\hat{\text{SSD}}(V_x, V_y) = \sum_{x,y} \left( I_2(x,y) - I_1(x,y) + V_x I_x + V_y I_y \right)^2$$

Where $I_x$ and $I_y$ are the spatial derivatives (gradients) of the image with respect to $x$ and $y$.

Furthermore, if $I_1$ and $I_2$ are frames captured with a very small time difference between them, we can denote the temporal difference as $I_t = I_2(x,y) - I_1(x,y)$. This simplifies our approximation to:

$$\hat{\text{SSD}}(V_x, V_y) = \sum_{x,y} \left( I_t + V_x I_x + V_y I_y \right)^2$$

To find the minimum of this quadratic function, we take the derivatives with respect to $V_x$ and $V_y$ and set them to zero. This yields a neat system of linear equations in the form of $Av = b$:

$$\begin{pmatrix} \sum I_x^2 & \sum I_x I_y \\ \sum I_x I_y & \sum I_y^2 \end{pmatrix} \begin{pmatrix} V_x \\ V_y \end{pmatrix} = - \begin{pmatrix} \sum I_x I_t \\ \sum I_y I_t \end{pmatrix}$$

#### The Catch: Matrix Invertibility

This linear system yields a unique solution if and only if the matrix $A$ is invertible.

- For an **infinite line**, all the spatial gradients point in the same direction, making the matrix $A$ have a rank of 1.
- Because a rank-1 matrix is not invertible, there are an infinite number of solutions.
- If you map out this infinite family of solutions mathematically, **it traces out exactly the same constraint line** we derived in human psychophysics!
    

> 🛠️ _Self-Study Tip: Try calculating this by hand or writing a quick script for a trivial case (like no motion, or a simple single-pixel line shift). Watching the constraint line emerge directly out of image gradients is incredibly satisfying!_

### The Computational View (Bringing back Marr)

Let’s map this computer vision approach back onto Marr's framework:

- **What is the problem?** Estimating the underlying velocity field across multiple changing image frames.
- **Why is it difficult?** Because we need a way to mathematically formalize how a physical velocity $v$ transforms pixel intensities across time.
- **What assumptions make it solvable?** The **Brightness Constancy Constraint**.

At the _Algorithmic Level_, various techniques can be deployed to minimize this error function—ranging from the classic Lucas-Kanade system of linear equations to modern deep learning models (like Flownet or RAFT) that many of you may recognize from advanced machine learning courses.

> 📝 **Note:** The basic Lucas-Kanade algorithm shown here is highly foundational and assumes very small movements. In modern applications, engineers use hierarchical "coarse-to-fine" image pyramids to handle large velocities. To see how these principles are expanded to modern, real-world systems, check out this classic highly-cited text: [An Iterative Image Registration Technique with an Application to Stereo Vision (Lucas & Kanade)](https://www.ri.cmu.edu/pub_files/pub3/lucas_bruce_d_1981_1/lucas_bruce_d_1981_1.pdf).

## Summary

- An infinite line moving with velocity $v$ defines a **constraint line** in velocity space.
- When two different line orientations overlap (forming a plaid pattern), they can be perceived as moving together via a **coherent motion** vector.
- Computers estimate motion based on **brightness constancy**. The **Lucas-Kanade algorithm** solves this efficiently by local linearization and solving systems of linear equations.
- Ultimately, **computers and humans face the exact same mathematical uncertainties** (the aperture problem) when extracting motion from the physical world.

## Quiz


**Question 1:** **True or False:** For any two images generated by two infinite lines of different orientations moving with velocity $v$, the IOC velocity will always have the minimum SSD.

**Answer: FALSE.** 
**Explanation:** This statement is only true **if we assume strict brightness constancy**.
 - **If brightness constancy holds:** The physical movement doesn't change pixel intensities, meaning the true IOC velocity perfectly matches the frames and yields an $\text{SSD} = 0$ (the absolute minimum).
 - **In the real world (Without brightness constancy):** If the lighting changes, a shadow passes by, or automatic camera gain kicks in between Frame 1 and Frame 2, the pixel values change. In this case, the true physical velocity ($v_{\text{IOC}}$) will **not** have an SSD of zero, and a completely different, incorrect velocity vector might end up mathematically minimizing the SSD instead.



**Question 2: True or False:** For any two images generated by two infinite lines of different orientations moving with velocity $v$, the basic Lucas-Kanade algorithm will compute the IOC velocity.

 **Answer: FALSE** : The basic Lucas-Kanade algorithm relies on a first-order Taylor series approximation, meaning it only computes the exact solution if the physical velocity displacement $v$ is extremely small (infinitesimal). If the movement between the two frames is large, the linear approximation breaks down, and the basic algorithm will fail to find the true IOC velocity without the aid of multi-scale image pyramids.

Here is the polished version of your Lecture 4 notes. I have cleaned up the typos, formalized the Bayesian equation and terminology (Likelihood, Prior, Posterior), and completed the sentence you left open to perfectly explain the philosophical and mathematical shift that Bayes' rule introduces.

## Lecture 4: Bayesian Decision Theory

### Introduction to the Framework

Before we continue deeper into our core visual material, we need to familiarize ourselves with a powerful mathematical framework that will help us analyze how the brain makes decisions under uncertainty. This framework is known as **Bayesian Decision Theory**.

While this field is vast enough to easily fill the scope of an entire course on its own, we are going to focus on a high-level overview of its most foundational concepts.

To understand what makes the Bayesian approach unique, let's look at a simple everyday example:

### The Thermometer Dilemma

Imagine you want to know the exact temperature outside. You open a local weather website, and it displays a digital reading of **$23.4^\circ\text{C}$**.

- **The Naive Answer:** The temperature outside is exactly $23.4^\circ\text{C}$ because that's what the sensor says.
- **The Bayesian Answer:** The actual outside temperature _depends_ on two competing factors: the reliability of that specific thermometer sensory data, and the historical climate patterns of your region for this exact time of year.
 ![[Pasted image 20260621095842.png]]
 _The website UI displaying a current reading of 23.4°C._

Now, let's look at a broader historic dataset:
![[Pasted image 20260621101056.png]]

 A historical climate graph showing that the average temperature for this specific day in April over the last fifty years is actually $20.0^\circ\text{C}$.

Suddenly, you have two different sources trying to tell you the truth:

1. **The Sensor Measurement:** $23.4^\circ\text{C}$
2. **The Historical Prior:** $20.0^\circ\text{C}$


How do we blend these two sources together? Under normal circumstances, you trust a functional thermometer more than historical averages. But what if the thermometer suddenly flashes **$50.0^\circ\text{C}$** in the middle of April? Your common sense immediately kicks in—you know $50.0^\circ\text{C}$ is historically nearly impossible for April, so you infer the thermometer is highly likely to be broken.

Bayesian decision theory provides the exact mathematical language to weigh these two forces against one another.

### Bayes' Rule: The Mathematics of Inference

To formalize this, let $Y$ be our observed measurement (the data from the world) and let $X$ be our variable of interest (the true state of the world we want to find). Bayes' Rule is written as:

$$P(X|Y) = \frac{P(X)P(Y|X)}{P(Y)}$$

To understand what this means, we break it down into its core functional components:

- **$P(X|Y)$ — The Posterior Probability:** The probability that the true state of the world is $X$, _given_ that we just observed measurement $Y$. This is what we are trying to calculate.
- **$P(Y|X)$ — The Likelihood:** The probability that the sensor would return measurement $Y$ _if the true state_ of the world actually were $X$. This represents the noise and reliability of our physical hardware.
- **$P(X)$ — The Prior Probability:** Our baseline internal belief about the world before we even look at a sensor. This is built from history, experience, and environmental constraints (e.g., "It's rarely $50^\circ\text{C}$ in April").
- **$P(Y)$ — The Normalizing Constant:** The total probability of observing measurement $Y$ across all possible states of the world.

To finish the thought: This formula means that the actual state of the world given a measurement **is not simply equivalent to the measurement itself, but is a fluid compromise between the physical evidence we observe ($The\ Likelihood$) and our foundational expectations of what makes sense ($The\ Prior$).** If our sensor is incredibly precise, the Likelihood dominates, and the Posterior shifts toward the measurement. If our sensor is highly noisy or returns an absurd anomaly, our Prior dominates, and our brain dismisses the data as an error.


### The Mathematical Case: Gaussian Distributions

Let’s look at a concrete mathematical case where both our prior knowledge and our sensory noise follow a normal (Gaussian) distribution.

Suppose our prior belief about the true state $X$ is distributed around a historical mean $\mu$ with a prior variance $\sigma^2_p$:
$$X \sim N(\mu, \sigma^2_p)$$
Next, suppose our sensor measurement $Y$ is a noisy estimate centered around the true state $X$ with a measurement variance $\sigma^2$:
$$Y|X \sim N(X, \sigma^2)$$
By multiplying the Gaussian prior and the Gaussian likelihood together according to Bayes' rule, the resulting posterior distribution $X|Y$ is also a Gaussian:
$$X|Y \sim N(\mu_{x|y}, \sigma^2_{x|y})$$
Where the new posterior mean $\mu_{x|y}$ is calculated as:
$$\mu_{x|y} = \frac{\frac{1}{\sigma^2}Y + \frac{1}{\sigma^2_p}\mu}{\frac{1}{\sigma^2} + \frac{1}{\sigma^2_p}}$$
If we rewrite this by defining **precision** as the inverse of variance ($w = \frac{1}{\sigma^2}$ and $w_p = \frac{1}{\sigma^2_p}$), the equation simplifies into a beautifully elegant form:
$$\mu_{x|y} = \frac{wY + w_p\mu}{w + w_p}$$
This demonstrates that the Bayesian estimation is fundamentally a **weighted average**. It automatically shifts more weight toward whichever distribution is more certain (i.e., has less noise / higher precision). Furthermore, this Bayesian estimator is mathematically **optimal**, meaning it is guaranteed to minimize the expected squared error.

### Tying the Math Back to the Thermometer

Let’s look at how this formula behaves when we push the reliability of our thermometer to its logical extremes:

- **The Perfect Instrument ($\sigma^2 \to 0$):**
    If the thermometer is exceptionally high-grade and has virtually zero noise, its precision approaches infinity ($w \to \infty$). In the weighting formula, the prior term drops out completely:$$\mu_{x|y} \to Y = 23.4^\circ\text{C}$$
    The brain completely trusts the measurement and ignores history.
    
- **The Broken Instrument ($\sigma^2 \to \infty$):**
    
    If the thermometer is highly unstable, cheap, or completely broken, its measurement noise approaches infinity, meaning its precision drops to zero ($w \to 0$). The measurement term completely vanishes from the equation:$$\mu_{x|y} \to \mu = 20.0^\circ\text{C}$$
    The brain completely dismisses the faulty sensor data and falls back entirely on its historical climate expectations.

Ultimately, Bayesian inference provides a mathematically rigorous way to extract a stable, accurate perception of reality even when we are forced to navigate the world using deeply noisy instruments.

Here is the polished, highly intuitive version of your notes for Lecture 4's application to motion. I have integrated the mathematics cleanly and completed the derivation path to show exactly how adding a prior for slow motion transforms the standard Lucas-Kanade equation into a beautiful explanation for why thin rhombuses trick our eyes.

### Connecting Bayesian Decision Theory to Motion Perception

Now that we have a solid grasp of the Bayesian framework, let's see how it elegantly resolves a classic conflict in human motion perception.

Recall the two competing methods we discussed for combining multiple moving lines: **Vector Average (VA)** and the **Intersection of Constraints (IOC)**. Both are mathematically sound frameworks, and each successfully predicts human perception under specific visual scenarios.

However, we run into a major dilemma when these two theories do not agree on the final direction of motion.

 ![[Pasted image 20260621104911.png]]
_A vector diagram showcasing a geometry where the Vector Average (VA) arrow points horizontally, while the Intersection of Constraints (IOC) point sits far off to the side, pointing diagonally._

In a scenario like the one above, where the two theories predict completely different directions, what does a human actually perceive?

### The Moving Rhombus Experiment

To see how the brain breaks this tie, consider the classic moving rhombus experiment:

> 🔬 **Interactive Check:** Open the [Moving Rhombus Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiment3.html). For the best effect, zoom in closely on your screen so that the outer edges of the window disappear, leaving only the moving rhombus itself visible.

If your visual system behaves like most people's, you will witness a fascinating shift in perception:

- **Thick Rhombuses:** Appear to move diagonally, tracking the **IOC** solution.
- **Thin/Narrow Rhombuses:** Appear to move strictly horizontally, shifting away from the IOC and tracking the **Vector Average**.

Why does changing the mere _thickness_ of a shape alter the fundamental algorithm the brain uses to see it? One could lazily argue that the brain randomly selects different algorithms for different shapes, but this is deeply unsatisfying. It fails to answer _why_ the visual system does this, and it doesn't give us a way to simulate the underlying mechanism.

### The Bayesian Unified Motion Model

Instead of assuming the brain bounces between two separate algorithms (VA and IOC), we can use **Bayesian Decision Theory** to model this behavior using a single, unified equation.

To build this model, we establish two core, human-centric assumptions:

1. **Noisy Measurements:** Local motion signals extracted along edges are inherently ambiguous and noisy.
2. **The Slow-Motion Prior:** As Hans Wallach noted, the human visual system possesses a strong biological preference for slower velocities over faster ones.

Let's translate these assumptions into a formal probabilistic framework:

#### 1. The Likelihood (Constant Intensity Assumption)

First, we look at the standard optical flow constraint error function $C(\vec{v})$, which measures how poorly a velocity vector $\vec{v} = (v_x, v_y)$ fits the constant brightness constraint across local image gradients:
$$C(\vec{v}) = \sum_{x,y,t} w(x,y,t)(I_x v_x + I_y v_y + I_t)^2$$

Assuming the noise in our visual measurements is Gaussian, the probability of observing a particular set of image gradients given a velocity $\vec{v}$ (The Likelihood) is expressed as:
$$P(I(x,y) | \vec{v}) = \alpha e^{-\frac{C(\vec{v})}{2\pi \sigma^2_n}}$$

Where $\sigma^2_n$ represents the variance of our sensory measurement noise.

#### 2. The Slow-Motion Prior

Next, we formalize our internal bias for slow motion. We model this as a Gaussian distribution centered at zero velocity ($\vec{v} = 0$) with a variance of $\sigma^2_p$:
$$P(\vec{v}) = \alpha e^{-\frac{\|\vec{v}\|^2}{2\sigma^2_p}}$$

#### 3. The Posterior Probability

By multiplying our likelihood and our prior together according to Bayes' rule, we get our final posterior probability:
$$P(\vec{v} | I) = \alpha \left( \prod_{x,y} P(I(x,y) | \vec{v}) \right) P(\vec{v})$$

**To finish the thought:** This means that finding the velocity we actually perceive is not just a matter of minimizing image gradient errors (maximizing the Likelihood). Instead, **the brain searches for a velocity vector that balances a low image-reconstruction error with a low absolute speed.** 

---

### The Elegant Mathematical Solution

To find the most likely perceived velocity ($\vec{v}^*$), we maximize the posterior probability, which is mathematically equivalent to minimizing the negative log-posterior.

Let's bring back our gradient matrix $M$ and temporal difference vector $b$ from the Lucas-Kanade algorithm:

$$M = \begin{pmatrix} \sum I_x^2 & \sum I_x I_y \\ \sum I_x I_y & \sum I_y^2 \end{pmatrix}, \quad b = -\begin{pmatrix} \sum I_x I_t \\ \sum I_y I_t \end{pmatrix}$$

When we differentiate our Bayesian objective function (which now includes the slow-motion prior term) and set it to zero, the slow-motion prior acts as a **regularization term** added directly onto the diagonal of our gradient matrix. This gives us the final Bayesian motion estimation:

$$\vec{v} = \left( M + \frac{\sigma^2_n}{\sigma^2_p} I \right)^{-1} b$$

Where $I$ is the identity matrix, and $\frac{\sigma^2_n}{\sigma^2_p}$ is the ratio of sensory noise to prior certainty.

### How This Explains the Rhombus Illusion

This single, elegant equation completely explains why thick and thin rhombuses look different:

- **Why Thin Rhombuses look like Vector Average:**
    
    A thin, narrow rhombus has very short edges, meaning it provides very little visual contrast data. Because the signal-to-noise ratio is incredibly low, our sensory noise is very high ($\sigma^2_n \to \infty$). In our equation, as $\sigma^2_n$ blows up, the regularization term dominates, **pulling the perceived velocity heavily toward zero (slower speeds).** This strong gravitational pull toward the origin forces the vector away from the distant, fast diagonal IOC point and snaps it straight to the shorter, more conservative horizontal Vector Average path!
    
- **Why Thick Rhombuses look like IOC:**
    
    A thick rhombus provides long, prominent edges with highly reliable, strong visual data. Because the sensory data is clear, the measurement noise is tiny ($\sigma^2_n \to 0$). The regularization fraction vanishes, reducing the equation right back to the standard Lucas-Kanade/IOC solution. The brain completely trusts the visual information and perceives the true, fast diagonal motion.

Rather than running separate algorithms, the human brain uses a single, beautifully optimized Bayesian framework that constantly dials between data-driven geometry (IOC) and internal survival expectations (Slow-Motion Prior) based entirely on how clean the visual signal is!

### Conclusions

To wrap up our discussion on Bayesian Decision Theory and its applications to motion, we can summarize the core insights into a few powerful takeaways:

- **The Integration vs. Segmentation Dilemma:** Analyzing motion in the real world constantly forces our visual system to decide whether to integrate multiple local motion signals together (treating them as one coherent moving object, like the thick rhombus) or segment them into separate entities.
- **Minimal Assumptions, Maximum Results:** The Bayesian model operates on a surprisingly minimal set of ecological constraints: it assumes local physical measurements are inherently ambiguous/noisy, and that things in our environment generally tend to move slowly and smoothly.
- **Remarkable Biological Agreement:** When we calculate the mathematically optimal interpretation under these exact constraints, the results land in remarkable agreement with actual human psychophysical data.
- **Demystifying Visual Illusions:** Rather than proving our brains are faulty, complex visual illusions (like the thin rhombus or varying plaid behaviors) are actually the side-effect of a single, highly optimal computational strategy doing its job under noisy conditions.
    

## Quiz

### Question 1: The Plaid Contrast Challenge

![[Pasted image 20260621121616.png]]

 _Scenario:_ Both of the plaid patterns shown in the image are physically moving at the exact same velocity. Plaid **a** is high-contrast (black and white), while Plaid b is low-contrast (light gray and dark gray).
 
**Question:** Which plaid will appear to move faster to a human observer?

#### Answer Breakdown

**Plaid 'a' (High-Contrast) will appear to move faster.**

- **The Mathematical Explanation:** Because Plaid **b** has very low contrast, the image gradients ($I_x, I_y$) are weak, resulting in a low signal-to-noise ratio and high sensory noise ($\sigma^2_n \to \infty$).
- In our Bayesian regularization formula $\vec{v} = \left( M + \frac{\sigma^2_n}{\sigma^2_p} I \right)^{-1} b$, a larger noise value $\sigma^2_n$ strengthens the pull of the slow-motion prior. This weights the posterior heavily toward zero velocity, causing the human brain to underestimate the speed of the low-contrast plaid.
- Because Plaid **a** has crisp, reliable contrast, its noise is minimal, allowing the brain to perceive its true, faster speed.

### Question 2: The Ultrasound Estimation

 **Scenario:** We measure a fetus's head size using an ultrasound and get a value of **$10.0\text{ cm}$**. We know that ultrasound measurements are noisy. From prior clinical data, we know that a fetus at this specific gestational stage has a mean head size of **$15\text{ cm}$** with a standard deviation of $3$.
 **Question:** Will the final Bayesian estimate be _below_, _above_, or _equal to_ $10.0\text{ cm}$?

#### Answer Breakdown

**The Bayesian estimate will be ABOVE $10.0\text{ cm}$.**

- **The Mathematical Explanation:** Let's look at the variables given:
    - Sensor Measurement ($Y$) = $10.0\text{ cm}$
    - Prior Mean ($\mu$) = $15\text{ cm}$
- Recall our Gaussian Bayesian posterior mean formula:$$\mu_{x|y} = \frac{wY + w_p\mu}{w + w_p}$$
- Because the ultrasound instrument is explicitly stated to be noisy, its precision weight ($w$) is finite. Therefore, the prior weight ($w_p$) acts as a statistical magnet. It pulls the posterior mean away from the raw sensor reading ($10.0\text{ cm}$) and shifts it upward toward the historical average of $15\text{ cm}$.
- As a result, the calculated optimal estimate will sit somewhere between $10.0\text{ cm}$ and $15\text{ cm}$ (e.g., around $11$ or $12\text{ cm}$ depending on the exact sensor variance), which is strictly **above $10.0\text{ cm}$**.

Congratulations on wrapping up the first major pillar of the course! You've officially conquered Motion Processing. Now, it's time to transition into our second core pillar: **Color Processing**.

Because color is an incredibly massive topic, we aren't going to jump straight into the full rainbow. Instead, we are going to strip things down to the basics and start with "black and white" vision—which includes all the varying shades of gray. And just like we did with motion, we will begin our journey with **psychophysics**.

## Lecture 5: Achromatic Vision & Brightness Perception

### Simultaneous Contrast

Let's kick things off with a classic visual puzzle. Look at the two inner gray squares below:
![[Pasted image 20260621123007.png]]
Even though the two inner squares have the exact same physical pixel intensity (they reflect the same amount of light), they look completely different to your eyes.

- The gray square on the **dark background** looks significantly lighter.
- The gray square on the **light background** looks significantly darker.

> 🔬 **Interactive Check:** If your brain is trying to tell you this is a trick, go prove it to yourself using the [Simultaneous Contrast Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiment4.html).

This phenomenon is formally known as the **Simultaneous Contrast Effect**. It tells us something fundamental about computational vision: **Human brightness perception is not pixel-perfect.** Your brain doesn't act like a light meter measuring absolute luminance; instead, it computes brightness _relatively_, evaluating an object based on its surrounding context.

### Biological Explanation: Lateral Inhibition

To explain why this happens, scientists looked at the hardware of the early visual system. They discovered a neural mechanism called **Lateral Inhibition**.

Cells in the retina and early visual cortex have a specific "center-surround" architecture. A neuron's firing rate is determined by a tug-of-war: light hitting the exact center of its receptive field _excites_ the cell, while light hitting the surrounding area _inhibits_ (suppresses) it.

This hardware setup perfectly explains the simultaneous contrast effect:
- The inner square on the white background receives a massive amount of surrounding light, causing heavy neural inhibition. The brain gets a suppressed signal, making the square look **darker**.
- The inner square on the black background receives almost no surrounding light, causing minimal inhibition. The neuron fires freely, making the square look **lighter**.

This low-level wiring also explains classic illusions like the **Hermann Grid** (where illusory gray dots appear at the white intersections of a grid) and various color-bleeding phenomena.

![[Pasted image 20260621123626.png]] _Diagrams demonstrating the Hermann Grid_

### The Boundary of Biology: The Gestalt Critique

If Lateral Inhibition were the final, absolute truth of brightness perception, it should predict everything perfectly. But consider this famous counter-experiment involving a uniform gray ring placed over a split black-and-white background:

 ![[Pasted image 20260621124004.png]]

According to lateral inhibition, the left half of the ring (on the white background) should look dark, and the right half (on the black background) should look light. But it doesn't! We perceive the entire ring as a single, uniform shade of gray. The brain overrides the local center-surround signals.

This is exactly where **Gestalt Psychology** comes into play.

_Gestalt_ is a German word that translates roughly to "form" or "unified whole." In vision science, Gestalt theory refers to the brain's automatic, top-down tendency to organize raw visual inputs into distinct objects, surfaces, and scenes rather than disconnected pixels.

Because your brain instantly groups the ring into a single, continuous object, it enforces a rule of global consistency. It concludes, _"This is one solid gray ring sitting on a split background,"_ completely overriding the local lateral inhibition signals. When you split the ring down the middle with a line, breaking that object unity, the simultaneous contrast illusion instantly snaps back into place!



## Summary

- **Contextual Brightness:** Our perception of brightness is highly relative and depends heavily on the surrounding background context.
- **Low-Level Hardware:** Early biological explanations relied on **lateral inhibition** (an implementation-level mechanism), which works well for simple layouts but fails for complex shapes.
- **High-Level Grouping:** **Gestalt theory** explains that our brain organizes visual scenes into unified surfaces and objects (an algorithmic-level approach), overriding local pixel-level discrepancies.


## Lecture 5 (Continued): The Computational View of Brightness

### The Physics of Light: Lambert's Law

To find the true computational explanation (Marr's first level) for why our brain processes brightness the way it does, we must look at the physics governing how light interacts with objects in a scene.

What actually determines the intensity of light that hits our retina?

![[Pasted image 20260621125212.png]]

When light interacts with a matte, non-shiny (Lambertian) surface, the intensity of light reaching our eye ($I$) is defined by three physical variables:

- $L$: The ambient illumination (the amount of light reaching the surface from a light source).
- $\rho$ (Albedo): The reflectance factor of the surface material (the intrinsic fraction of light it reflects, ranging from $0$ to $1$).
- $\theta$: The angle between the surface normal vector and the incoming light source.

Mathematically, this relationship is expressed as:

$$I = \rho L \cos\theta$$

### Why the Problem is Mathematically Ill-Posed

If our goal is to perceive the true physical property of an object—whether it is naturally white or naturally black—what we actually need to extract is the **albedo** ($\rho$).

- An albedo of $\rho \approx 0.1$ reflects only 10% of light, which we perceive as a dark surface like asphalt.
- An albedo of $\rho \approx 0.9$ reflects 90% of light, which we perceive as a bright surface like fresh snow.

**Here is why the problem is incredibly difficult:** Our eyes only have access to the final product, $I$ (the pixel intensity). We are given a single number ($I$) which is the result of multiplying three unknown numbers ($\rho$, $L$, and $\cos\theta$). Mathematically, trying to solve for three unknowns with just one equation is an **ill-posed problem** with an infinite number of solutions.

Yet, the human visual system effortlessly separates these layers, allowing us to instantly tell whether an object looks bright because it has a high albedo ($\rho$), or simply because a strong light ($L$) is shining directly on it.

### A Practical Example: Adelson's Checker Shadow Illusion

To see this computational decomposition in action, consider the famous checkerboard shadow illusion:
![[Pasted image 20260621130808.png]]

In this illusion, Square A sits out in the open, while Square B sits inside a shadow cast by a cylinder. Astonishingly, both squares output the _exact same_ light intensity to your eye ($I_A = I_B$). Your brain, however, automatically factors out the illumination context using a brilliant bit of unconscious algebra:

1. The visual system recognizes that Square B is sitting in a shadow, meaning its incoming illumination is lower than Square A's ($L_A > L_B$).
2. We know from physics that:$$I_A = \rho_A L_A \quad \text{and} \quad I_B = \rho_B L_B$$
3. Since our eyes measure $I_A = I_B$, substituting the equations yields:$$\rho_A L_A = \rho_B L_B$$
4. Because the brain already established that $L_A > L_B$, the only mathematically consistent reality is that **$\rho_A < \rho_B$**.

By solving this internal equation, your brain successfully estimates that Square B must have a significantly higher intrinsic reflectance ($\rho$) than Square A. It overrides the raw pixel values to present you with the true physical layout of the board: Square B is a light check, and Square A is a dark check!

## Summary

- **Contextual Brightness:** Our perception of brightness is not a direct readout of pixel intensity; it depends heavily on spatial context.
- **Lambert’s Law:** The light energy reaching our eye follows the physical equation $I = \rho L \cos\theta$.
- **The Computational Goal:** The core mission of the visual system is to **estimate the intrinsic reflectance ($\rho$) from the compounded input ($I$)** by making smart assumptions about illumination ($L$) and scene geometry ($\cos\theta$).

## Quiz

### Question 1: Receptive Fields vs. Gestalt

![[Pasted image 20260621131251.png]]

 **Question:** (T/F) According to the lateral inhibition explanation, A should be perceived as brighter than B.

#### Answer: TRUE

- **Explanation:** Lateral inhibition is a strictly localized, low-level mechanism.
    
    - The outer edge of Region A is bordered by a very dark background, meaning its surrounding receptive fields receive very little light and generate minimal neural inhibition. This causes the neurons tracking A to fire intensely.
    - Region B is bordered by a highly reflective, light background, causing massive surrounding inhibition that suppresses the neural signal.
    - Therefore, purely based on the hardware mechanics of lateral inhibition, A must be perceived as brighter than B (even though the Gestalt object unity of the ring tries to override this globally).

### Question 2: Surface Angles and Reflected Light

 **Question:** (T/F) Suppose the sun is directly above us. A piece of paper lying flat on the ground will reflect more light than a piece of paper that is at 45 degrees to the ground.

#### Answer: TRUE

- **Explanation:** This is a direct application of Lambert’s Law ($I = \rho L \cos\theta$).
    
    - When the sun is directly overhead, the incoming light rays are parallel to the surface normal of a flat piece of paper. The angle $\theta$ between the light source and the normal is $0^\circ$, and since $\cos(0^\circ) = 1$, the reflected light intensity is maximized ($I = \rho L$).
    - For the paper tilted at $45^\circ$, the angle becomes $\theta = 45^\circ$. Since $\cos(45^\circ) \approx 0.707$, the tilted paper physically receives and reflects roughly 30% less light than the flat paper.

## Lecture 6 retinex

