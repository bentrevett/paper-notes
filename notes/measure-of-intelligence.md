# The Measure of Intelligence (2019)

Francois Chollet

---

We need a way to define and evaluate intelligence so we can use it as a feedback signal for developing AI systems. 

Current systems measure *skill* and not *intelligence*. 

Current advancements in AI have been due to a very narrow target goal and a definite way to measure progress, i.e. sentiment analysis from a labelled dataset, whereas we need a way to measure intelligence. The Turing Test is a bad example as they remove the objective definition and measurement of intelligence. 

A definition: "intelligence measures an agent's ability to achieve goals in a wide range of environments". Definitions usually have emphasis on task specific and generalization. Thus, high skill across many tasks. The tasks will not be known in advance and thus the agent would have to be able to learn how to handle new tasks. This is called skill acquisition.

Traditional AI focused on intelligence as a collection of task-specific skills - with the aim to encode human skills into formal rules and encode human knowledge into explicit databases. The new breed of AI (machine/deep learning) focuses on intelligence as the general ability to acquire new skills through learning.

Datasets are good for driving progress in AI but models built to solve them do not posesses any human intelligence. 

Humans can only display high skill at a specific task if they have the ability to efficiently acquire skills in general. Nobody is born learning how to play chess, but a human became the best chess player in the world they would be regarded as intelligent. We would imply that they used their general intelligence to acquire the specific skill over a period of time which reflects their ability to acquire many other possible skills in the same way.

For non-humans, there is no task T that the system being great at T would prove they are intelligent., unless T is a broad range of multiple tasks.

Generalization is seeing how well a model performs on inputs not part of its training data. It is the "ability to handle situations (tasks) that differ from previously encountered situations". The last bit is ambiguous, what counts as a previously encountered situation? There are different types of generalization as well as different degrees of generalization. 

A system is *robust* if it can correctly classify images in the test set. A *flexible* system is a L5 self-driving car or a robot able to enter a random kitchen and make a cup of coffee. We are not at this stage yet.

Figure 1 shows how AI progress is being made (or is aiming to be made). 

Psychometrics has intelligence tests for humans. They evaluate across a broad set of abilities as opposed to task-specific skills. An *ability* is an abstract concept rather than a measurable *skill*. Ability is measured across a set of tasks, rather than a single one. These tasks should have not been seen by the test taker before. The GLUE dataset is a start towards this, but researchers know the tasks in advance and we train on domain specific data. 

Systems such as AlphaStar and OpenAI Five are still task-specific. Achievements in those tasks have taught us nothing about how to solve intelligence. Humans go from general to specific, but going from specific to general has not been solved yet. Hard-coded prior knowledge or adding more task-specific training data does not help generalization. A system aiming for general-purpose intelligence should be trained to optimze directly for flexibility and generality.

Modern deep learning systems are local-generalization systems, similar to a locality-sensitive hashtable. They can show great skill ata task, but doing so requires huge amount of training data (think RL). This is not possible for self-driving cars.

Humans like to anthropomorphize intelligence and only call animals intelligent when they do something similar to what humans do. General intelligence is not binary property which a system either has or does not have. AI systems should focus on human intelligence. The large majority of human skills are not innate, but the mind is not a blank slate. Humans have priors such as meta-learning and high-level knowledge which make humans intelligent. AI systems without these priors would be at a disadvantage when comparing their intelligence against human intelligence, but the systems should not have more hard-coded knowledge than humans. 

The 4 human priors are:
- objectness (enviroments can be parsed into objects) and physics (those objects follow sensible rules)
- agentness (some objects are agents) and goal-directedness (these agents have their own goals)
- arithmetic (natural innate ability for simple maths)
- geometry and topology (distance, orientation, navigation around 2D/3D space)

**The intelligence of a system is a measure of its skill-acquisition efficiency over a scope of tasks, with respect to priors, experience and generalization difficulty**

Figure 2 shows that the intelligent system should have a task specific(?) *skill program*, such as being able to complete a new level in a video game. Is the skill program only for that one game? Or for all possible games? The intelligent system is used to train the skill program. During evaluation the intelligent system is not used, only the skill program.

Outcomes of their formalism:
- A high-intelligence system can generate high-skill solution programs for high generalization difficulty tasks with minimal experience and priors.
- Measure of intelligence is tied to scope and skills levels required by tasks in the scope.
- Skill is not possessed by an intelligent system, it is a property of the output from the intelligent system (a skill program). High skill is not high intelligence.
- Intelligence must have learning and adaptation.
- Measure of intelligence is tied to curriculum optimization

When applied to CS:
- computational efficiency of the skill program
- computational efficiency of the intelligent system
- time-efficiency
- energy efficiency
- risk efficiency

Introduces the ARC dataset. 400 training tasks, 600 evaluation tasks. Each task has around 3 training examples and 1 test example. The inputs and outputs are all grids. When solving, the system has access to the demonstration example for that task. ARC uses the following priors (i.e. a system is assumed to have these):
- object cohesion
- object persistence
- object influence via contact
- goal-directedness (grids can be thought of as having start and end points)
- numbers and counting (which object appears the most/least?)
- geometry and topology (lines, rectangular shapes, symmetry, scaling, distortions, copying)

Deep learning systems cannot tackle this dataset yet due to its focus on broad generalization and few-shot learning.

Think of ARC as a program synthesis benchmark. Generate a program that satisfies a high-level specification from input/output pairs for that desired program.

Weaknesses of ARC:
- generalization not quantified
- validity not established
- dataset size and diversity is limited
- evaluation is not granular
- does it really capture the desired priors?

Alternatives:
- broader generalization by seeing how well system trained on a game could solve variants of the game with novel gameplay mechanics
- generate new tasks with an adversary