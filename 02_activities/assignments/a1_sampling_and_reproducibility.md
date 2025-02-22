# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

#  Author: Fiona He

```
Please write your explanation here...
This code simulates an infection and contact tracing process across two event types (weddings and brunches).This model simulates the infection and contact-tracing process with three key sampling stages: infection selection, primary tracing, and secondary tracing. Each introduces a form of bias, particularly in how secondary tracing overemphasizes certain events. I'll identify and describe all stages where sampling occurs, referencing the functions used, sample sizes, sampling frames, and distributions.
Sampling Stages in the Model
1. Infection Sampling (np.random.choice)
   Function Used: np.random.choice
   Sampling Frame: All 1000 individuals attending events (200 wedding attendees, 800 brunch attendees).
   Sample Size: int(len(ppl) * ATTACK_RATE), which is 1000 * 0.10 = 100 people.
   Procedure: A simple random sample (SRS) without replacement is drawn from all individuals to determine who gets infected.
   Underlying Distribution: Uniform distribution (each person has an equal probability of being infected).
   Relevance to Bias: This models a random attack rate, meaning some events may have disproportionately more infections just by chance.

2. Primary Contact Tracing (np.random.rand)
   Function Used: np.random.rand
   Sampling Frame: The subset of individuals who were infected in step 1 (approximately 100 people).
   Sample Size: A fraction (TRACE_SUCCESS = 0.20) of the infected people.
   Procedure: For each infected person, a random number is drawn from a uniform distribution between 0 and 1. If this number is below 0.20, the person is marked as traced.
   Underlying Distribution: Bernoulli (each infected individual has a 20% independent probability of being traced).
   Relevance to Bias: Since tracing is probabilistic and not event-specific, this can introduce bias if certain events have higher infection rates but lower traceability.

3. Secondary Contact Tracing (Threshold-Based Sampling)
   Function Used: .value_counts() and filtering based on SECONDARY_TRACE_THRESHOLD
   Sampling Frame: Events where at least 2 infected people were successfully traced in step 2.
   Sample Size: Events that meet or exceed the threshold (SECONDARY_TRACE_THRESHOLD = 2).
   Procedure: If an event has at least 2 traced cases, all infected attendees of that event are marked as traced.
   Underlying Distribution: Depends on the distribution of primary traces, but can create a cluster-based effect.
   Relevance to Bias: This amplifies tracing in events where multiple people were already identified, potentially overrepresenting infections from certain event types.

4. Repeated Sampling Across Simulations (simulate_event loop)
   Function Used: List comprehension [simulate_event(m) for m in range(50000)]
   Sampling Frame: Each run independently samples infections and traces.
   Sample Size: 50,000 independent simulations.
   Procedure: The simulation repeats the infection and tracing process 50,000 times to estimate overall proportions of infections and traces from weddings.
   Underlying Distribution: The aggregate distribution of results will approximate a normal distribution due to the central limit theorem.
   Relevance to Bias: This allows for statistical estimation of the extent of bias introduced by contact tracing.

Relation to Andrew Whitby’s Blog Post
The blog post discusses biases in contact tracing—specifically, how secondary tracing inflates the apparent role of certain events in spreading infections. The findings from the code align with Whitby’s blog post, demonstrating how contact-tracing methodology can distort our understanding of transmission dynamics. This code aligns with that discussion in several ways:

Overrepresentation of Traced Events:

As secondary contact tracing applies only when at least 2 infections are traced, events with high initial traces (like weddings, which have fewer attendees but a higher likelihood of clustering infections) may be overrepresented.
This mirrors the bias in the blog post, where large groups can become artificially prominent in contact-tracing data.
Randomness vs. Systematic Bias:

While primary tracing is unbiased (each infected person has an independent 20% chance of being traced), secondary tracing introduces systematic bias by reinforcing the representation of events already flagged.
The blog post highlights how "superspreader events" may be statistical artifacts of contact-tracing methods, rather than truly riskier.
Amplification of Certain Events in Reporting:

The final histogram of traced cases shows a higher proportion of infections attributed to weddings compared to the initial infections.
This reflects how contact tracing amplifies the role of certain events in reported statistics, leading to a perception bias.

```
After running the code, I got the picture as:
![alt text](image.png)

After modifying the number of repetitions in the simulation to 1000 (from the original 50000), the histograms change significantly comparing the former one.
This indicates the script is not reproducible due to random sampling. I made the change on the code by adding random seed as:
def simulate_event(m, seed=42):
 np.random.seed(seed + m) 
 ......
 results = [simulate_event(m, seed=42) for m in range(1000)]
 ......

 See the updated script named as 'whitby_covid_tracing_debug.ipynb'

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
