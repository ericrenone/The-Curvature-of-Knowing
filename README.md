# The Curvature of Knowing

### Ronald Fisher, Shun'ichi Amari, and the geometry that measures how much a signal can tell you

---

<img width="703" height="1024" alt="image" src="https://github.com/user-attachments/assets/cbfa4fc7-3637-4334-9320-30233c3d2967" />

---

**Abstract.** Two men who never met built, sixty years apart, the single most important instrument for deciding how much a measurement is worth. Ronald Fisher (1890–1962), a near-blind Cambridge mathematician who thought in pictures because he could not read equations comfortably, defined in 1922 the quantity that now carries his name: the amount of information an observation carries about an unknown, equal to the curvature of the likelihood at its peak. In the same paper he defined *sufficiency*, a statistic that loses none of that information, which is the first rigorous definition of a lossless kernel. Shun'ichi Amari (born 1936), one of five students in a newly founded mathematical-engineering course in Tokyo, recognized in the 1970s and 1980s that Fisher's quantity is not a number but a *metric*: it turns every family of probability distributions into a curved space, and on that space the shortest paths, the flat directions, the projections and the gradients all have statistical meaning. He named the field information geometry, and in 1998 he drew from it the natural gradient, the rule that learning should move in the geometry Fisher defined rather than in the arbitrary coordinates a model happens to be written in. That rule now sits inside reinforcement learning, second-order optimizers for deep networks, and the defense against catastrophic forgetting. This document reconstructs both lives and the lineage between them (Edgeworth, Fisher, Rao, Chentsov, Efron, Amari), shows what each man did with the idea, places both in the two-protocol framework of the companion essays (Fisher information is the fidelity measure; sufficiency is the kernel; the Cramér–Rao bound is the floor; natural gradient is descent in the fidelity metric), connects the result directly to the adaptive decoder in the ARALE repository, and records the 2024 episode in which a Nobel Prize went to a network Amari had published ten years before its namesake, which is the companion essays' law about credit operating on a living man.

---

## Contents

1. [A Man Who Could Not See and a Man Who Was One of Five](#1-a-man-who-could-not-see-and-a-man-who-was-one-of-five)
2. [What Fisher Information Is](#2-what-fisher-information-is)
3. [Ronald Fisher: A Life in Variance](#3-ronald-fisher-a-life-in-variance)
4. [Shun'ichi Amari: A Life in Curvature](#4-shunichi-amari-a-life-in-curvature)
5. [The Lineage: From a Number to a Space](#5-the-lineage-from-a-number-to-a-space)
6. [What Amari Did With Fisher](#6-what-amari-did-with-fisher)
7. [The Natural Gradient, and What Descends From It](#7-the-natural-gradient-and-what-descends-from-it)
8. [Where It Meets the Decoder](#8-where-it-meets-the-decoder)
9. [Credit: The Network With Two Names](#9-credit-the-network-with-two-names)
10. [The Two Protocols, Stated in Fisher's Terms](#10-the-two-protocols-stated-in-fishers-terms)
11. [The Composite Timeline](#11-the-composite-timeline)
12. [Disputed and Unconfirmed Points](#12-disputed-and-unconfirmed-points)
13. [Sources](#13-sources)

---

## 1. A Man Who Could Not See and a Man Who Was One of Five

Ronald Fisher's eyesight was poor enough that the British Army rejected him in 1914. His tutors at Cambridge, where he had taken a First in mathematics in 1912, had taught him with as little written work as possible, and he developed the habit of solving problems by visualizing them in space rather than manipulating symbols on paper. In 1915, asked to find the sampling distribution of the correlation coefficient, a problem that had defeated Karl Pearson's laboratory with algebra, Fisher solved it in a few pages by treating a sample of *n* observations as a single point in *n*-dimensional space and reading the answer off the geometry. He was twenty-five.

Hold that fact. The man who defined statistical information was a geometer by disability. He would spend the next forty years writing the quantity down as an integral, and the geometry he could see would remain implicit in his equations until a Japanese engineer born the year Fisher published *The Design of Experiments* made it explicit.

Shun'ichi Amari was born in Tokyo in 1936. In 1958 he was admitted to a newly established course in Mathematical Engineering at the University of Tokyo that accepted five students a year, and in his 2025 Kyoto Prize lecture, titled "My Life Has Been Lucky," he dated his good fortune to that room. His master's and doctoral theses, completed in 1960 and 1963, were on diakoptics, the method of the Hungarian-American engineer Gabriel Kron for solving a large electrical network by tearing it into pieces, solving each, and reassembling the solution. His doctoral title was *Diakoptics of Information Spaces*. Before he had touched a neuron or a probability distribution, Amari was already working on how to cut a system into parts without losing what mattered about the whole, which is the compression problem in its purest engineering form.

Fisher died in Adelaide in July 1962. Amari received his doctorate in 1963. They never met, never corresponded, and the bridge between them was a single student of Fisher's, C. R. Rao, who in 1945 wrote the paper that turned Fisher's number into a distance. What Amari did with that distance is the subject of this document.

---

## 2. What Fisher Information Is

Suppose you have a model: a family of probability distributions, one for each value of an unknown parameter θ. You observe data. How much does the data tell you about θ?

Fisher's answer, in words: it depends on how sharply the likelihood of the data changes as you vary θ. If small changes in θ change the probability of what you saw a great deal, the data pins θ down tightly. If the likelihood is flat across a wide range of θ, the data barely constrains it. Fisher measured the sharpness as the expected squared slope of the log-likelihood, which, under mild conditions, equals the negative expected curvature at the peak. High Fisher information means a sharp peak; low information means a blunt one.

Four properties, each stated in the Wikipedia article reproduced in this repository, carry the whole of what follows.

**The bound.** The Cramér–Rao inequality (Rao 1945, Cramér 1946) says that no unbiased estimator of θ can have variance smaller than the reciprocal of the Fisher information. This is the floor. It plays the role in estimation that Shannon's entropy plays in coding: a limit below which no cleverness can go.

**Sufficiency.** Fisher's 1922 paper defined a sufficient statistic as a summary of the data that carries all of the data's information about θ; the Fisher information of any statistic is at most the Fisher information of the full sample, with equality exactly when the statistic is sufficient. This is the earliest rigorous definition of a lossless kernel: a reduced object from which everything the archive knew about the question can be recovered.

**Additivity.** Independent observations add information. The chain rule for Fisher information mirrors the chain rule for entropy.

**Reparametrization.** Change the coordinates on the parameter space and the Fisher information transforms by the square of the Jacobian, in the vector case by J^T I J. That is the transformation law of a metric tensor. Fisher information is not a scalar property of a model; it is a Riemannian metric on the space of distributions. This single fact is the door Amari walked through.

And one relation that makes the metric mean something: the Fisher information is the second-order coefficient of the Kullback–Leibler divergence between nearby distributions. Relative entropy, the information-theoretic cost of mistaking one distribution for another, is locally a quadratic form, and Fisher information is that form. So the Fisher metric measures *distinguishability*: two distributions are far apart when data can easily tell them apart. In the vocabulary of the companion essays, it is the fidelity metric, the one that says how much of the truth survives a given amount of noise.

---

## 3. Ronald Fisher: A Life in Variance

| Year | Event |
|---|---|
| 1890 | Born 17 February, East Finchley, London; a surviving twin; father an auctioneer |
| 1904 | Mother dies; father's business fails within two years |
| 1909–1912 | Scholarship to Gonville and Caius College, Cambridge; First in mathematics; taught largely without written work because of his eyesight |
| 1911 | Founding chairman of the Cambridge University Eugenics Society |
| 1912 | First paper on what would become maximum likelihood (the "absolute criterion") |
| 1915 | Derives the exact distribution of the correlation coefficient by n-dimensional geometry |
| 1918 | Introduces the word *variance* and the analysis of variance; reconciles Mendel with Darwin |
| 1919 | Rothamsted Experimental Station, where he stays fourteen years analyzing crop data back to 1842 |
| 1922 | "On the mathematical foundations of theoretical statistics": defines information, sufficiency, efficiency, likelihood |
| 1925 | *Statistical Methods for Research Workers*; p = 0.05 and 1.96 enter the world; "Theory of statistical estimation" formalizes the information quantity |
| 1930 | *The Genetical Theory of Natural Selection*; the fundamental theorem; the last third is eugenics |
| 1933 | Succeeds Karl Pearson as Galton Professor of Eugenics, University College London |
| 1935 | *The Design of Experiments*; the lady tasting tea; the null hypothesis |
| 1936 | The Iris data set; the paper arguing Mendel's data were too good |
| 1943 | Balfour Professor of Genetics, Cambridge; recruits Cavalli-Sforza in 1948 |
| 1945 | His student C. R. Rao publishes the Cramér–Rao bound and the Fisher–Rao metric |
| 1950s | Publicly disputes the link between smoking and lung cancer; consults for tobacco firms |
| 1952 | Knighted; opposes the UNESCO Statement on Race |
| 1957 | Retires to Adelaide, CSIRO |
| 1962 | Dies 29 July after surgery for colon cancer; interred in St Peter's Cathedral, Adelaide |
| 2020 | Caius removes his stained-glass window; Rothamsted renames a building; UCL removes his name from a research centre |

Three threads in that table run into the present.

**The two variances.** In 1918 Fisher coined *variance* for the spread of a trait. In 1922 he defined information as the variance of the score. In 1930 his fundamental theorem of natural selection stated that the rate at which a population's fitness increases equals its genetic variance in fitness. The same man, three times, said the same thing in three fields: the capacity of a system to change, to learn, to be pinned down, is a variance. Fisher information is the variance of how much the data's likelihood responds to the parameter; evolutionary speed is the variance of how much fitness responds to genotype. A reader of the companion essays will recognize this as the frontier stated in Fisher's language: there is no information without spread, and no selection without variance.

**The geometric instinct.** The 1915 derivation was not a trick; it was how Fisher saw. He never wrote the geometry of his information quantity down as geometry, and when Rao did in 1945, the paper was received as a theorem about bounds rather than as a description of a curved space. The space waited forty years for someone whose thesis had been about tearing information spaces into pieces.

**The author and the archive.** Fisher's eugenics was not incidental. He chaired the Cambridge society at twenty-one, held the Galton chair, edited the *Annals of Eugenics*, drafted sterilization legislation (voluntary, he insisted), and opposed the 1950 UNESCO statement on the grounds that human groups differ in innate capacity. He wrote a testimony for the German eugenicist Otmar von Verschuer after the war and corresponded with him for decades. Historians disagree about the label: one trust of his memorial foundation called him an anti-racist conservative; the historian Richard Evans called his views indicative of racism. What is not disputed is what the institutions did in 2020. They removed the window, the building name and the centre name, and kept the analysis of variance, the p-value, maximum likelihood, the design of experiments and Fisher information, every one of which is used every day in every quantitative discipline. The companion essay on Japan describes the state's handling of a dissenting author with the phrase *honor the object, mute the author*. Fisher's case is the mirror image: the object was never in question, and the author was removed from the wall. In both cases the archive survived its maker. The tools are the kernel; the man was the archive; the institutions kept the one that travels.

---

## 4. Shun'ichi Amari: A Life in Curvature

| Year | Event |
|---|---|
| 1936 | Born in Tokyo |
| 1958 | Graduates from the Mathematical Engineering course at the University of Tokyo, one of five students admitted |
| 1960 | Master of Engineering: *Topological and Information-Theoretical Foundation of Diakoptics and Codiakoptics* |
| 1963 | Doctor of Engineering: *Diakoptics of Information Spaces*; Associate Professor, Kyushu University (to 1967) |
| 1967 | "A theory of adaptive pattern classifiers," *IEEE Trans. Electronic Computers*: stochastic gradient descent for a layered classifier; with his student H. Saito, a multilayer perceptron trained by it |
| 1967 | Associate Professor, University of Tokyo (to 1981) |
| 1968 | *A Geometrical Theory of Information* (in Japanese), the title given to a field seventeen years before it existed |
| 1972 | "Learning patterns and pattern sequences by self-organizing nets of threshold elements," *IEEE Trans. Computers*: the associative-memory network later known as the Hopfield network |
| 1972 | "Characteristics of random nets of analog neuron-like elements": statistical mechanics of randomly connected networks |
| 1974 | A method of statistical neurodynamics |
| 1977 | "Dynamics of pattern formation in lateral-inhibition type neural fields," *Biological Cybernetics*: the Amari neural field equation |
| 1978 | *Mathematical Theory of Nerve Nets* (in Japanese) |
| 1981 | Professor, University of Tokyo (to 1996) |
| 1982 | "Differential geometry of curved exponential families: curvatures and information loss," *Annals of Statistics*: the dual α-connections |
| 1985 | *Differential-Geometrical Methods in Statistics*, Springer Lecture Notes in Statistics 28 |
| 1992 | IEEE Neural Networks Pioneer Award |
| 1993 | *Methods of Information Geometry* with Hiroshi Nagaoka (in Japanese; English, AMS/Oxford, 2000) |
| 1994–1997 | Group Director, Brain Information Processing Group, RIKEN Frontier Research Program |
| 1995 | Japan Academy Prize; information geometry of the EM and em algorithms |
| 1995–1996 | With Cichocki and Yang, "A new learning algorithm for blind signal separation" (NIPS 8): natural-gradient independent component analysis; the Amari distance |
| 1996 | Professor Emeritus, University of Tokyo |
| 1997 | IEEE Emanuel R. Piore Award; Group Director, Brain-Style Information Systems, RIKEN Brain Science Institute |
| 1998 | "Natural gradient works efficiently in learning," *Neural Computation* |
| 2000 | With Park and Fukumizu, adaptive natural-gradient learning for multilayer perceptrons |
| 2003 | C&C Prize; Center Director, RIKEN Brain Science Institute (to 2008) |
| 2008–2018 | Senior Advisor, RIKEN BSI |
| 2011 | Order of the Sacred Treasure |
| 2012 | Person of Cultural Merit |
| 2016 | *Information Geometry and Its Applications*, Springer |
| 2017–2018 | Honorary Scientist, then Honorary Science Advisor, RIKEN |
| 2019 | Order of Culture |
| 2021 | Specially Appointed Professor, Teikyo University |
| Oct 2024 | Nobel Prize in Physics to Hopfield and Hinton; the Japanese Neural Network Society and the *Asahi Shimbun* object |
| Nov 2025 | Kyoto Prize in Advanced Technology; lecture "My Life Has Been Lucky"; honorary doctorate, TU Berlin |
| Mar 2026 | Kyoto Prize Symposium, San Diego; Kyoto Prize at Oxford, May 2026 |

Read the table with the companion essays' thirty-year fuse in mind. Amari said in his prize lecture that each of his three main lines of work took ten or twenty years to gain recognition. The 1967 classifier was recognized in the 1980s. The 1972 memory was recognized under another man's name in 1982 and given a Nobel in 2024. The 1982 geometry became a field in the 2000s and an optimizer inside industrial deep learning in the 2010s. He is eighty-nine as this is written, and the honors arrived in the order the companion essays predict: the state first (Cultural Merit 2012, Order of Culture 2019), the world last (Kyoto 2025).

---

## 5. The Lineage: From a Number to a Space

Fisher information became a geometry in five steps, each by someone who saw a little more of what Fisher had drawn.

**Edgeworth, 1908.** Francis Ysidro Edgeworth, in a series of papers on the probable errors of frequency constants, computed quantities that are recognizably Fisher's information for specific cases. Fisher did not cite him; later historians (Pratt 1976, Stigler 1978) established the priority. The number existed before the name, which is the usual order.

**Fisher, 1922 and 1925.** The definition, sufficiency, efficiency, and the role of the quantity in maximum likelihood. The 1922 paper is routinely described as the most influential in twentieth-century mathematical statistics.

**Rao, 1945.** Calyampudi Radhakrishna Rao, then twenty-five, a student of Fisher's at Cambridge (he would complete his doctorate under him in 1948), published "Information and the accuracy attainable in the estimation of statistical parameters." The paper contains the Cramér–Rao bound. It also contains the observation that Fisher's information matrix, being positive-definite and transforming as a tensor, defines a Riemannian metric on the parameter space, and that one can measure the distance between two distributions as the geodesic length in that metric. This is the Fisher–Rao metric, and it is the first time anyone wrote the geometry down.

**Chentsov, 1972.** Nikolai Chentsov, in Moscow, proved that the Fisher metric is, up to a constant factor, the *only* Riemannian metric on families of distributions that is invariant under sufficient statistics (more precisely, under the Markov morphisms that preserve information). This is the uniqueness theorem. It says that Fisher's number is not one reasonable choice among several; it is the single geometry that respects the notion of "losing no information." Chentsov also introduced a one-parameter family of affine connections on the same space, which would become central to Amari's work.

**Efron, 1975.** Bradley Efron, at Stanford, defined the *statistical curvature* of a one-parameter model and showed that models which are not exponential families are curved in a precise sense, and that the curvature measures how much information the maximum-likelihood estimator loses in finite samples. The title of Amari's 1982 paper, "curvatures and information loss," answers Efron's.

**Amari, 1982 and 1985.** The dual connections, the α-family, the dually flat spaces, and the name.

---

## 6. What Amari Did With Fisher

The step from Rao to Amari is the step from a metric to a *connection*, and it deserves a plain explanation because it is where the mathematics stops being a formula and becomes a way of thinking.

A metric tells you the length of a path. A connection tells you what "straight" means: how to carry a direction along a path without turning it. On the surface of the Earth, the metric is given by distances along the ground, and the straight lines are great circles. On a family of probability distributions, Rao's metric is Fisher's, but there is a question Rao did not ask: what are the straight lines?

Amari's answer, published in the *Annals of Statistics* in 1982 and expanded in the 1985 Springer volume, is that there are two natural answers, and they are dual to each other. In an *exponential family* (Gaussians, Poissons, Bernoullis, anything whose log-density is linear in its natural parameters), the straight lines in the natural parameters are one notion of flatness, which he called e-flat. In a *mixture family* (convex combinations of fixed distributions), straight lines in the mixture weights are another notion, m-flat. These two notions of straightness are different, and they are coupled through the Fisher metric in exactly the way that a vector space and its dual are coupled: parallel transport along one and then back along the other is the identity. Amari defined a continuous family of connections indexed by a parameter α, with α = +1 the exponential connection, α = −1 the mixture connection, and α = 0 the ordinary Riemannian (Levi-Civita) connection of the Fisher metric. The ±1 pair is *dually flat*: a space that is flat in both senses at once admits two coordinate systems, two sets of straight lines, and a Pythagorean theorem for the Kullback–Leibler divergence in which the right angle is taken between an e-geodesic and an m-geodesic.

Why this matters is best seen in what it does. In 1995 Amari showed that the EM algorithm, the workhorse of estimation with missing data, is a pair of alternating projections in this geometry: an e-projection onto the model and an m-projection onto the data, each along the straight lines of the opposite type, and each guaranteed to decrease the divergence by the Pythagorean relation. What had been a clever iterative recipe became a picture: two curved surfaces and a point bouncing between them along the two kinds of straight line. The same picture governs mean-field approximations in physics, belief propagation in graphical models, and variational inference in Bayesian machine learning. Amari's 2016 book collects the applications across statistics, signal processing, optimization, quantum information and neuroscience.

Here is the plainest way to say what Amari did with Fisher. Fisher defined how far apart two distributions are *locally*. Rao integrated that into a global distance. Amari asked what it means to go *straight* from one distribution to another, found that there are two answers, and found that almost every algorithm statisticians and engineers had built by hand was secretly moving along one of them.

---

## 7. The Natural Gradient, and What Descends From It

The 1998 paper's title is a plain sentence: natural gradient works efficiently in learning. The idea is as plain.

When you train a model by gradient descent, you compute the direction in which the loss decreases fastest and take a step. But "fastest" is measured in some geometry, and ordinary gradient descent measures it in the Euclidean geometry of the parameters, which is to say in the arbitrary coordinates the model happens to be written in. Rescale one parameter by a factor of ten and the "steepest" direction changes, though nothing about the model's predictions has. The parameters are coordinates on the space of distributions, not distances in it, and Euclidean steps in coordinates are steps in a geometry that has nothing to do with what the model does.

The natural gradient replaces the Euclidean geometry with Fisher's. The steepest-descent direction in the Fisher metric is the ordinary gradient multiplied by the inverse Fisher information matrix. The resulting step is invariant to reparametrization: it moves the model the same way in distribution space no matter how the parameters are written. Amari proved that online natural-gradient learning is *Fisher-efficient*: asymptotically it attains the Cramér–Rao bound, which is to say it extracts from each example as much information about the true parameters as any unbiased estimator could. He also showed, with Park and Fukumizu in 2000, how to estimate the inverse Fisher matrix adaptively, online, so that the method could run on a multilayer perceptron without inverting a matrix at every step, and that natural gradient escapes the plateaus that trap ordinary gradient descent near singular regions of the parameter space, where the Fisher matrix degenerates.

That last observation, the singular regions, opened a field of its own. Neural networks, mixtures, hidden Markov models and most of the models in modern machine learning are *singular* statistical models: their Fisher information matrix is not positive-definite everywhere, so the classical asymptotics fail. Sumio Watanabe's algebraic-geometric theory of singular learning, and the widely applicable information criterion that came out of it, are built on exactly this failure of Fisher's regularity condition, and Amari's work on plateaus and singularities in the 2000s is part of the same program. The geometry that explained why learning worked also explained, precisely, where it stalled.

The descendants of the 1998 paper are now part of the furniture of the field:

- **Natural policy gradient** (Kakade, 2002) applied the idea to reinforcement learning, and Trust Region Policy Optimization (Schulman et al., 2015) made it practical at scale by constraining each policy update to a small Kullback–Leibler step, which is a Fisher-metric ball.
- **K-FAC** (Martens and Grosse, 2015) and its successors approximate the inverse Fisher matrix of a deep network by Kronecker factors, making second-order natural-gradient training feasible for networks with millions of parameters; Martens's 2020 survey in the *Journal of Machine Learning Research* is the standard account.
- **Elastic weight consolidation** (Kirkpatrick et al., 2017) uses the diagonal of the Fisher information as a measure of how important each parameter is to a task already learned, and penalizes changes to important parameters when learning the next task. Fisher information, which measures how much the data tells you about a parameter, is run backward to measure how much a parameter tells you about the data, and the result is a defense against forgetting.
- **Natural evolution strategies** and **natural-gradient variational inference** carry the idea into black-box optimization and Bayesian approximation.
- **Independent component analysis.** Amari, Cichocki and Yang's 1995–96 algorithm for blind source separation was the first application: the unmixing matrix lives on a Lie group, the ordinary gradient ignores that, and the natural gradient on the group gives an algorithm that is simpler, faster and equivariant. The Amari distance, a measure of how close a recovered unmixing is to the truth, is still the standard benchmark.

The companion essay on the compression instinct quotes a cartoonist's confession that he chose his criteria by what could be easy, and reads it as engineering: remove every recurring cost that the reader will not notice. The natural gradient is that instinct applied to learning. Ordinary gradient descent wastes steps in directions the model's output does not care about; the natural gradient spends each step where the distribution actually moves. "Works efficiently" is the whole claim, and it is the same claim.

---

## 8. Where It Meets the Decoder

The ARALE repository describes a cryogenic decoder that tracks a drifting 8×8 channel with a decision-directed normalized least-mean-squares update, and carries its channel estimate either as an explicit inverse updated by the Sherman–Morrison identity or as a QR factorization updated by rotations. The companion essay on that decoder placed those two representations in the kernel/archive framework. Fisher and Amari supply the missing third reading, and it is exact.

For a linear model with Gaussian noise, which is the decoder's channel model y = Hx + w, the Fisher information matrix for the channel parameters is the correlation matrix of the input x. The natural gradient for this model is therefore the ordinary gradient multiplied by the inverse input correlation, and the online algorithm that implements it is recursive least squares. The covariance recursion of recursive least squares is the Sherman–Morrison update. So the decoder's explicit-inverse mode is, to the letter, online natural-gradient learning of the channel, and the instability that the decoder's authors measure (error growth proportional to the square of the condition number, per the 2025 result of Hashemi and Nakatsukasa) is the instability that Amari, Park and Fukumizu addressed in 2000 by estimating the inverse Fisher matrix adaptively rather than exactly.

The decoder's default mode, NLMS, normalizes the update by the scalar ‖x‖² instead of the full inverse correlation. That is the natural gradient under the approximation that the input correlation is a multiple of the identity, which is nearly true for the decoder's codebook (the 240 minimal vectors of E₈ are, by the lattice's symmetry, isotropically distributed), and it is why the decoder gets most of the benefit of the natural gradient with none of the matrix. The companion essay called the scalar normalization "the hair left uninked." It is also the diagonal Fisher approximation, the same economy that lets elastic weight consolidation run on a billion parameters.

And the decoder's error threshold, convergence below about one percent symbol errors and divergence above ten, is a statement about Fisher information: a decision-directed update uses the decoded point as if it were the truth, and each wrong decision is a sample that carries negative information about the channel. Below the threshold the positive samples dominate and the estimate converges at the Fisher-efficient rate; above it the loop is extracting information about a channel that does not exist. Fisher gave the quantity, Amari gave the geometry, and the decoder's Monte Carlo gives the number.

---

## 9. Credit: The Network With Two Names

On 8 October 2024 the Nobel Prize in Physics was awarded to John Hopfield and Geoffrey Hinton for foundational discoveries enabling machine learning with artificial neural networks. The citation named Hopfield's 1982 paper on associative memory. In 1972 Amari had published a learning rule for a self-organizing network of threshold elements that stores patterns as stable states and recovers them from corrupted input, which is the same network. Commentators from Durham University and the *Asahi Shimbun* said so within weeks; the Japanese Neural Network Society, congratulating the laureates, added that the role of Japanese pioneers in building the foundations must not be forgotten. Kunihiko Fukushima's 1979–80 Neocognitron, the first convolutional network, was the other name raised. Amari's 1967 stochastic-gradient training of a layered classifier was raised in connection with Hinton's 1986 backpropagation paper.

The companion essays contain a law about this: credit flows to the compressor, not to the source. A kernel that travels under a name becomes that name's property. "Hopfield network" is a kernel; it is two words that any graduate student can carry, attached to a 1982 paper in a journal physicists read, written in the vocabulary of spin glasses, by a man already famous for other work. Amari's 1972 paper is an archive: an IEEE transactions article, in the vocabulary of threshold logic, by an engineer in Tokyo, a decade before the field it belonged to had a name. The archive was correct and complete. The kernel was what traveled.

The historical record contains a precise precedent, in Fisher's own column of the ledger. Edgeworth computed Fisher information in 1908. Fisher named it in 1922 and did not cite him. The quantity is called Fisher information in every textbook on earth, and the priority is a footnote in Stigler. Fisher was the compressor; Edgeworth was the source. Amari, sixty years later, stands where Edgeworth stood.

What is notable is how Amari has handled it. In his Kyoto Prize lecture he said that he had never imagined his early work at Kyushu would one day be connected to a Nobel Prize in Physics, and he titled the lecture with the word *lucky*. The Wikipedia article, by contrast, now calls the 1972 network the Amari–Hopfield network, which is the archive asserting itself in the only way an archive can: by attaching a second name to the kernel after the fact. Whether the two-word kernel will accept a third word is, on the evidence of the companion essays, doubtful. Kernels do not take additions. But the Kyoto Prize, the Order of Culture, and the honorary doctorate from Berlin in November 2025, presented by a former visiting scholar of Amari's, are what the fidelity protocol can offer: not the name on the thing, but the institution's record of who made it.

---

## 10. The Two Protocols, Stated in Fisher's Terms

The companion essay *Compression and Fidelity* built its framework from scripture, logos and cartoons. Fisher and Amari let it be restated as theorems.

| Framework term | Fisher's term | The statement |
|---|---|---|
| The fidelity metric | Fisher information | How distinguishable nearby distributions are; locally the Hessian of relative entropy |
| The floor | Cramér–Rao bound | No estimator's variance is smaller than 1/I(θ) |
| The kernel | Sufficient statistic | A reduction of the data with I_T = I_X; losing nothing about θ |
| The frontier | I_T ≤ I_X | Every reduction of the data loses information unless it is sufficient; the loss is measurable |
| The threshold | Singularity | Where the Fisher matrix degenerates, the classical guarantees fail (Watanabe; Amari on plateaus) |
| Additivity of the archive | Chain rule | Independent observations add; n samples carry n times the information |
| Invariance of the kernel | Chentsov's theorem | The Fisher metric is the unique geometry preserved by information-losing-nothing maps |
| Learning in the fidelity geometry | Natural gradient | Steepest descent measured by Fisher, not by coordinates; attains the floor |
| The two kinds of straight | Dual connections | e-geodesics and m-geodesics; the EM algorithm as alternating projection |
| Credit flows to the compressor | Eponymy | Fisher information (Edgeworth 1908); Hopfield network (Amari 1972) |

One more correspondence closes the loop with the first of the companion essays. The decoder essay argued that source coding and channel coding, compression and fidelity, are dual problems in Shannon's sense, one geometry seen from two sides. Amari's dual connections are that duality made explicit on the space of distributions: the exponential family is the natural home of maximum-likelihood estimation (fidelity: pin the parameter down), and the mixture family is the natural home of mixing and averaging (compression: represent many as one), and the two are coupled through the Fisher metric so that a projection in one is a straight line in the other. The cartoonist and the studio, the dub and the no-cuts policy, the Sherman–Morrison update and the QR factorization, are all pairs of this type. Amari drew the picture in 1982, and it was a picture Fisher could have seen, if he had been able to see.

---

## 11. The Composite Timeline

| Year | Fisher line | Amari line | Lineage |
|---|---|---|---|
| 1890 | Fisher born, London | | |
| 1908 | | | Edgeworth computes the information quantity for special cases |
| 1912 | Fisher's First; first likelihood paper | | |
| 1915 | Correlation coefficient by n-dimensional geometry | | |
| 1918 | *Variance* coined; ANOVA | | |
| 1922 | Information, sufficiency, efficiency defined | | |
| 1925 | *Statistical Methods for Research Workers* | | |
| 1930 | *Genetical Theory*; fundamental theorem | | |
| 1933 | Galton Professor of Eugenics, UCL | | |
| 1935 | *The Design of Experiments* | | |
| 1936 | Iris data; Mendel paper | Amari born, Tokyo | |
| 1945 | | | Rao: Cramér–Rao bound; Fisher–Rao metric |
| 1946 | | | Cramér; Jeffreys prior from Fisher information |
| 1948 | Rao completes doctorate under Fisher | | Shannon's theory of communication |
| 1951 | | | Kullback–Leibler divergence |
| 1958 | | Enters Mathematical Engineering, one of five | |
| 1962 | Fisher dies, Adelaide | | |
| 1963 | | D.Eng., *Diakoptics of Information Spaces*; Kyushu | |
| 1967 | | Adaptive pattern classifiers; SGD for layered nets | |
| 1968 | | *A Geometrical Theory of Information* (Japanese) | |
| 1972 | | Associative memory net; random nets | Chentsov's uniqueness theorem |
| 1975 | | | Efron's statistical curvature |
| 1977 | | Neural field equation | |
| 1982 | | Dual connections, *Annals of Statistics* | Hopfield's associative memory paper |
| 1985 | | *Differential-Geometrical Methods in Statistics* | |
| 1986 | | | Rumelhart, Hinton, Williams: backpropagation |
| 1993/2000 | | *Methods of Information Geometry* | |
| 1995 | | Japan Academy Prize; EM as projections; natural-gradient ICA | |
| 1998 | | "Natural gradient works efficiently in learning" | |
| 2002 | | | Kakade: natural policy gradient |
| 2003 | | C&C Prize; Director, RIKEN BSI | |
| 2012 | | Person of Cultural Merit | |
| 2015 | | | TRPO; K-FAC |
| 2016 | | *Information Geometry and Its Applications* | |
| 2017 | | | Elastic weight consolidation uses Fisher as importance |
| 2019 | | Order of Culture | |
| 2020 | Caius window removed; UCL centre renamed | | |
| 2024 | | | Nobel in Physics to Hopfield and Hinton; objections from Japan |
| 2025 | | Kyoto Prize; "My Life Has Been Lucky"; TU Berlin honorary doctorate | |
| 2026 | | Kyoto Prize Symposium, San Diego (March); Oxford (May) | |

---

## 12. Disputed and Unconfirmed Points

| Claim | Status | Record |
|---|---|---|
| Amari proposed "the first deep learning network" in 1967 | Framing contested | The claim is Schmidhuber's (2022); the 1967 paper trains a layered classifier by stochastic gradient descent; whether "deep learning" is the right description of a 1967 network is a matter of definition |
| Amari "anticipated backpropagation" | Partly | The 1967 work uses gradient learning in a multilayer classifier; the chain-rule algorithm named backpropagation was published by Werbos (1974) and popularized by Rumelhart, Hinton and Williams (1986) |
| Hopfield's 1982 paper is "mathematically equivalent" to Amari's 1972 | Asserted by commentators | Durham University / The Conversation (Nov. 2024); the networks share the Hebbian storage rule and energy-descent recall; whether Hopfield was aware of Amari's paper is not established here |
| Fisher did not cite Edgeworth | Documented | Savage (1976) and Pratt (1976) on the priority; Stigler (1978) |
| Fisher was an "anti-racist conservative" | Contested | Kevles's phrase, cited by the Fisher Memorial Trust's 2021 paper; Evans (2020) argues the opposite; the UNESCO 1950–52 record and the 1954 Gates letter are the primary evidence |
| Fisher's tobacco position was bought | Disputed | Yates and Mather (1963) defend his sincerity; Stolley (1991) argues conflict and personal habit biased him |
| Amari's exact birth date | Not confirmed here | Sources give 1936, Tokyo; no month is given in the Kyoto Prize profile |
| H. Saito was Amari's student and co-author in 1967 | Per Wikipedia citing Schmidhuber | Not independently verified |
| Chentsov's theorem applies to all statistical models | Scope | Proved for finite sample spaces; extended to broader classes by later authors (Ay, Jost, Lê, Schwachhöfer, 2017) |
| RLS is "exactly" natural gradient | True for the linear-Gaussian model | The Fisher matrix of y = Hx + w with respect to H is the input correlation; outside that model the identity is only approximate |
| The decoder's NLMS is a diagonal Fisher approximation | Interpretation | Exact if the input correlation is a multiple of the identity; the E₈ minimal-vector codebook is isotropic by symmetry, so the approximation is good for that codebook and not guaranteed for others |

---

## 13. Sources

**Fisher**
- Fisher, R. A., "Frequency distribution of the values of the correlation coefficient in samples from an indefinitely large population," *Biometrika* 10 (1915)
- Fisher, R. A., "On the mathematical foundations of theoretical statistics," *Phil. Trans. R. Soc. A* 222 (1922)
- Fisher, R. A., "Theory of statistical estimation," *Proc. Camb. Phil. Soc.* 22 (1925)
- Fisher, R. A., *Statistical Methods for Research Workers* (1925); *The Genetical Theory of Natural Selection* (1930); *The Design of Experiments* (1935)
- Box, Joan Fisher, *R. A. Fisher: The Life of a Scientist* (Wiley, 1978)
- Yates, F., and K. Mather, "Ronald Aylmer Fisher 1890–1962," *Biographical Memoirs of Fellows of the Royal Society* 9 (1963)
- Savage, L. J., "On rereading R. A. Fisher," *Annals of Statistics* 4 (1976); Pratt, J. W., "F. Y. Edgeworth and R. A. Fisher on the efficiency of maximum likelihood estimation," same issue
- Stigler, S. M., "Francis Ysidro Edgeworth, statistician," *JRSS A* 141 (1978); *The History of Statistics* (Harvard, 1986)
- Aldrich, J., "R. A. Fisher and the making of maximum likelihood 1912–1922," *Statistical Science* 12 (1997)
- Efron, B., "R. A. Fisher in the 21st century," *Statistical Science* 13 (1998)
- Bodmer, W., et al., "The outstanding scientist, R. A. Fisher: his views on eugenics and race," *Heredity* 126 (2021)
- Evans, R. J., "RA Fisher and the science of hatred," *New Statesman* (July 2020)
- UNESCO, *The Race Concept: Results of an Inquiry* (1952)
- Wikipedia, "Ronald Fisher" (rev. Aug. 31, 2026); "Fisher information" (rev. Sept. 27, 2026)

**Amari**
- Kyoto Prize, laureate profile and citation (2025): https://www.kyotoprize.org/en/laureates/shun-ichi_amari/
- Kyoto Prize Symposium, "My Life Has Been Lucky!" (San Diego, March 2026): https://kyotoprize-us.org/upcoming-events/shunichiamarilecture
- BIFOLD / TU Berlin, "Honorary Doctorate for Shun-ichi Amari" (Nov. 2025): https://www.bifold.berlin/news-events/news/view/news-detail/honorary-doctorate-for-prof-shun-ichi-amari
- Amari, S., "A theory of adaptive pattern classifiers," *IEEE Trans. Electronic Computers* 16 (1967)
- Amari, S., "Learning patterns and pattern sequences by self-organizing nets of threshold elements," *IEEE Trans. Computers* C-21 (1972)
- Amari, S., "Characteristics of random nets of analog neuron-like elements," *IEEE Trans. Syst. Man Cybern.* 2 (1972)
- Amari, S., "Dynamics of pattern formation in lateral-inhibition type neural fields," *Biological Cybernetics* 27 (1977)
- Amari, S., "Differential geometry of curved exponential families: curvatures and information loss," *Annals of Statistics* 10 (1982)
- Amari, S., *Differential-Geometrical Methods in Statistics*, Lecture Notes in Statistics 28 (Springer, 1985)
- Amari, S., and H. Nagaoka, *Methods of Information Geometry* (AMS/Oxford, 2000)
- Amari, S., "Information geometry of the EM and em algorithms for neural networks," *Neural Networks* 8 (1995)
- Amari, S., A. Cichocki, and H. H. Yang, "A new learning algorithm for blind signal separation," *NIPS* 8 (1996)
- Amari, S., "Natural gradient works efficiently in learning," *Neural Computation* 10 (1998)
- Amari, S., H. Park, and K. Fukumizu, "Adaptive method of realizing natural gradient learning for multilayer perceptrons," *Neural Computation* 12 (2000)
- Amari, S., *Information Geometry and Its Applications* (Springer, 2016)
- Schmidhuber, J., "Annotated History of Modern AI and Deep Learning," arXiv 2212.11279 (2022)
- Durham University / The Conversation, "Japanese scientists were pioneers of AI, yet they're being written out of its history" (Nov. 2024): https://theconversation.com/japanese-scientists-were-pioneers-of-ai-yet-theyre-being-written-out-of-its-history-243762
- Wikipedia, "Shun'ichi Amari" (rev. Aug. 4, 2026)

**The lineage**
- Edgeworth, F. Y., "On the probable errors of frequency-constants," *JRSS* 71 (1908), three parts
- Rao, C. R., "Information and the accuracy attainable in the estimation of statistical parameters," *Bull. Calcutta Math. Soc.* 37 (1945)
- Cramér, H., *Mathematical Methods of Statistics* (Princeton, 1946)
- Jeffreys, H., "An invariant form for the prior probability in estimation problems," *Proc. R. Soc. A* 186 (1946)
- Kullback, S., and R. A. Leibler, "On information and sufficiency," *Annals of Mathematical Statistics* 22 (1951)
- Chentsov, N. N., *Statistical Decision Rules and Optimal Inference* (Nauka, 1972; AMS translation 1982)
- Efron, B., "Defining the curvature of a statistical problem (with applications to second order efficiency)," *Annals of Statistics* 3 (1975)
- Ay, N., J. Jost, H. V. Lê, and L. Schwachhöfer, *Information Geometry* (Springer, 2017), for the generalized Chentsov theorem
- Watanabe, S., *Algebraic Geometry and Statistical Learning Theory* (Cambridge, 2009); "A widely applicable Bayesian information criterion," *JMLR* 14 (2013)

**Descendants of the natural gradient**
- Kakade, S., "A natural policy gradient," *NIPS* 14 (2002)
- Schulman, J., et al., "Trust region policy optimization," *ICML* (2015)
- Martens, J., and R. Grosse, "Optimizing neural networks with Kronecker-factored approximate curvature," *ICML* (2015); Martens, J., "New insights and perspectives on the natural gradient method," *JMLR* 21 (2020)
- Kirkpatrick, J., et al., "Overcoming catastrophic forgetting in neural networks," *PNAS* 114 (2017)

**The decoder**
- ARALE repository and `arale_model.py`: https://github.com/ericrenone/ARALE
- Hashemi, B., and Y. Nakatsukasa, on the stability of Sherman–Morrison updates (2025; follow-up 2026)
