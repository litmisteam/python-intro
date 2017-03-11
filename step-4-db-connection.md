# **DB Connection**

The goal of Step 4 in this tutorial is to introduce the audience to much more complex bottle topics. We will focus on both deeper template usage and interactions with the Bottle template interface as well as ibm-db-dbi connections to the local DB2 offering that comes with the partition of the IBM i offered through Litmis Spaces. This is not meant to make you an expert on DB2 operations, SQL queries, or template usage. Though I do hope to illustrate some of the core concepts of these topics as well as more general computer science topics to grant access to training that might be able to help or encourage you to work towards that mastery.

---

#### Templates \(Continued\)

In a way, this section is a necessary continuation of Step 2's introduction to the Bottle template engine \(SimpleTemplate\). Here though, we are going to go further in depth and make use of form data as well as rendering parameters for the templates. First, we need to create a file that represents the template we want to render when a particular action \(route\) occurs.



