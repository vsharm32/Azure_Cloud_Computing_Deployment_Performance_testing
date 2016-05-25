# Azure_Cloud_Computing_Deployment_Performance_testing_JMeter_Blazemeter
Deployment of Kunagi application on Microsoft Azure, performance testing using Apache Jmeter and Blazemeter, and creating provisioning strategies for improving the performance of application.

Here are the details of project requirements-:

CS441 Engineering Distributed Objects For Cloud
Computing Course Project
Total: 35% of your final grade
September 16, 2015
As part of taking CS441, you will work on a course project that allows
you to get good hands-on experience with deploying software applications
on cloud computing platforms. In this project, we focus on client-server
data-centric applications because of their widespread use. Specifically, the
front end of the application accepts requests from users (i.e., the input data),
the middle tier performs some business logic computations, and the backend
encapsulates data storage. For example, a user of a typical three-tier
webstore application browses items, puts them into a shopping cart, and
purchases selected items. We term the set of related client requests and
computations as a work unit or transaction. Each client request is the input
data for an application, and its performance behavior depends on the type
and the size of the input data, among other things. The performance of
the application is typically measured as its throughput, i.e., the number of
transactions per time interval that the application executes – it is inverse
to the measure of an average time per transaction. Effective provisioning
strategies will allocate resources to maintain a desired level of the throughput
for the application.
1 Provisioning Resources in the Cloud
Depending on the types of resources, their provisioning to applications has
different effect on the throughput. Consider scaling up this application
where more CPUs are added as new users arrive. Since the application
spawns a new thread for each user, adding CPUs and assigning newly
spawned threads to these CPUs will increase the throughput of the application.
At the same time, adding more RAM may not necessarily help.
On the other hand, if the database appears to be a main bottleneck, scaling
out this application by starting its new instance or a new instance of
1
its database may increase the throughput by parallelizing transaction processing.
Thus, resource provisioning in the cloud is a main technique for
maintaining a desired level of the throughput to guarantee some quality of
service for applications.
In a cloud computing model, software components run inside virtual machines
(VMs), which provide emulated physical machines. In general, the
cloud allocates different amounts of resources to different VMs and these resources
have different costs. For example, if a VM contains more components
that involve computationally intensive operations, the cloud scales it up by
assigning more CPUs and memory units. Alternatively, the cloud can scale
out this application by replicating VMs, thus enabling multiple requests to
be processed in parallel by these VMs that run these components.
That is, the cloud uses two main scaling operators: sres(r, a, i) and
sinst(a, i), where r is the type of a resource (e.g., memory, CPU), a is its
quantity, and i is the VM identifier. The scaling operator sres (de)allocates
the resource, r, in the quantity, a, to the VM, i, and the scaling operator
sinst (de)allocates a instances of the VM, i. Of course, different costs are
associated with these scaling operators. For example, allocating a new VM
is more expensive than assigning more CPU or RAM to an already running
VM. In theory, elastic clouds “know” when to apply these operators to
(de)allocate resources with high precision and efficiency.
2 The State-of-the-Art and Practice
Today, stakeholders typically deploy their applications in the cloud, using
ad-hoc scripts in which they encode the behavior of these applications and
rules on how the cloud should apply the scaling operators based on a coarse
collection of performance counters and otherwise “guesstimating” on how to
provision resources to their applications in the cloud. The Google Cloud, the
Amazon EC2 and Microsoft Azure clouds have issued guidelines for manually
load balancing their elastic clouds, directing their users to manually
specifying the conditions that trigger work routing and scaling operations.
The Amazon EC2’s documentation on auto scaling, for instance, states that
“use the ... Amazon CloudWatch command to create an alarm for each condition
under which you want to add or remove Amazon EC2 instances, and
specify the Auto Scaling Policy that you want the alarm to execute when that
condition is met. You can define alarms based on any metric that Amazon
CloudWatch collects. Examples of metrics on which you can set conditions
include average CPU utilization, network activity or disk utilization.”
2
The key takeaway here is that existing cloud providers understand that
their users often need to manually configure the cloud. Such manual activities
are tedious, error-prone and expensive, and clearly demonstrate that
clouds, such as Amazon’s EC2, have a long way to go in their quest for
better elasticity (see http://aws.amazon.com/autoscaling/).
Naturally, testers want to summarize the behavior of an Application Under
Test (AUT) concisely in terms of its inputs, so that they can select input
data that will lead to significantly increased resource consumption, thereby,
revealing performance problems. Descriptive rules for selecting test input
data play a significant role in software testing, where these rules approximate
the functionality of an AUT. For example, a performance rule for an
actual renter’s insurance application says that when convictedOfFraud is
true and deadboltInstalled is false, the throughtput of the AUT will
worsen. Useful descriptive rules often enable testers to build effective fault
revealing performance test cases.
A performance rule is a relation A ⇒ C, where A is an antecedent in
the form Ip  VIp • Iq  VIq • . . . • Ik  VIk , where  is one of the relational
operators and • stands for logical connectors and and or and VIm is the
value of the input Im. C ∈ {G, B}, where G and B stand for good and bad
throughputs correspondingly.
3 Goal of Course Project
A goal of this project is to discover provisioning strategies that most effectively
alleviate decreasing throughputs. You will search through a space of
possible cloud provisioning operations using your knowledge of the AUT’s
behavior for different input values, i.e., its performance rules. You will
obtain performance rules for your assigned AUT using performance experiments
that you will design and run in the cloud with Apache JMeter 1. For
example, if a software component involves computationally intensive operations
or requires a lot of memory, you will create provisioning rules that
enable the cloud to scale-up the VM in which the component runs by giving
it more CPU and memory units. If the component’s performance has
unacceptable latency, resulting from database interactions, then the cloud
could scale-out the VM that contains this database. These strategies can
be applied to the system and if additional testing shows performance improvements,
then you will create a new provisioning strategy that includes
the reallocation of resources to make the application scale up and/or out.
1http://jmeter.apache.org/
3
A provisioning strategy is a relation P ⇒ (R • R)∗, where P is a performance
rule and R • R is a resource provisioning scheme, where R ∈
{sres, sinst} are resource (de)allocation operators and • stands for logical
connectors and and or and ∗ is the Kleene star.
An example representation of a provisioning strategy for this rule is
(A, R)⇒ sres(P, 3, V Mi) ∧ sinst(2, V Mn), meaning that if we observe
inputs to the application, A, then the rule R holds, meaning that we will
triple the number of CPU units, P, that are assigned to the V Mi (i.e.,
scale up), and double the number of virtual machines, V Mn that run the
AUT (i.e., scale out). If this provisioning strategy is made available to the
cloud in advance, then when the application, A is executed with the input
values that satisfy the performance rule R, the cloud will provision resources
according to the designated provisioning strategy instead of waiting until the
performance of the application demonstrably worsens as is done in existing
clouds. Conversely, the cloud will deallocate resources to some baseline level
if no consequent is present based on the provisioning rules.
4 Course Project Guidelines
Your goal is to investigate your subject AUT to determine its performance
behavior for different inputs. You should run your AUT under load tests
with different clients that perform multiple transactions. You will obtain
URL requests that exercise different components of the AUT, and these
URLs that are sent to the AUT’s backend during this process using JMeter.
In exploratory random performance testing, multiple URLs are randomly
selected to form a transaction. Various constraints must be observed when
forming transactions. For example, the user action “checkout” is permitted
only when the user already logged into the system and the shopping chart
is not empty. Thus, if a URL representing “checkout” action is selected into
a transaction, then two other URLs representing “user logged in ”and “add
to shopping cart” must also be added into the same transaction.
I suggest that you limit the number of URLs in each transaction to
100, this number was chosen experimentally based on our observations of
industry application users who explored approximately 100 URLs before
switching to other activities. Increasing the number of certain URL requests
in transactions at the expense of not including other URL requests
may leads to increased workloads, and the goal of your experiments is to
show that you can eventually selects workloads (i.e., the number of users
performing concurrent transactions and the content of a transaction) that
4
lead to decreased throughput values. These workloads can be described
using performance rules, which you will record and explain. For each performance
rule, you will create provisioning strategies that will increase the
throughputs and you will explain why they work.
