# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Hui Li

```
Please write your explanation here...
Stages of Sampling in the Model
1. Population Generation
Function: simulate_event(m)
Sample Size: 1000 individuals (200 at weddings, 800 at brunches)
Sampling Frame: Individuals attending events (weddings and brunches)
Procedure:
The script creates a DataFrame ppl with 1000 individuals.
These individuals are assigned to either a wedding (200 individuals) or a brunch (800 individuals).
This represents the population for the simulation.
Code: 
events = ['wedding'] * 200 + ['brunch'] * 800
ppl = pd.DataFrame({
    'event': events,
    'infected': False,
    'traced': np.nan
})

2. Infection Sampling
Function: simulate_event(m)
Sample Size: Determined by ATTACK_RATE (10% of the population, i.e., 100 individuals)
Sampling Frame: Entire population of 1000 individuals
Underlying Distribution: Uniform random selection
Procedure:
Randomly selects 10% of the population to be infected using np.random.choice.
Marks these selected individuals as infected.
This simulates the random spread of infection within the population.
Code:
infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
ppl.loc[infected_indices, 'infected'] = True

3. Primary Contact Tracing Sampling
Function: simulate_event(m)
Sample Size: Determined by TRACE_SUCCESS (20% of infected individuals, i.e., 20 individuals)
Sampling Frame: Infected individuals
Underlying Distribution: Bernoulli distribution with a success probability of 0.20
Procedure:
For each infected individual, the script determines if they are traced with a probability of 20%.
This is done using np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS.
This step simulates the primary contact tracing process, where some infected individuals are identified through tracing.
Code:
ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS

4. Secondary Contact Tracing Sampling
Function: simulate_event(m)
Sample Size: Events with at least two traced cases
Sampling Frame: Events (weddings or brunches)
Procedure:
Counts the number of traced cases at each event.
Identifies events with at least two traced cases.
Marks all infected individuals at these events as traced.
This simulates secondary contact tracing, identifying additional infected individuals based on multiple traced cases at specific events.
Code:
event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

 After running the Python script, graph shows the distribution of true proportions of infections resulting from weddings, centered around 20% with some variation. The script simulates this accurately, producing a histogram that matches the blog post's description.

Comment on the reproducibility of the results:
1.To ensure the code is reproducible, we need to set a random seed both globally and within the function to make sure the random processes produce the same results every time the script is run: results = [simulate_event(m) for m in range(1000)]
2.By not setting the random seed inside the simulate_event function, we ensure that each simulation run uses the controlled randomness provided by the global seed.
The script, as modified, should produce the same output every time it is run, demonstrating reproducibility.

Although the specific numbers may not match those in Whitby's blog post exactly due to different random seeds and number of repetitions, the overall pattern (i.e., the observed proportion being higher than the true proportion) should be similar.
```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `sampling-and-reproducibility`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
