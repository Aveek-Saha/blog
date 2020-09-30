---
title: How to prepare for the TensorFlow Developer Certificate Exam
subtitle: A brief summary of what materials to study from and my experience giving the exam.
date: 2020-09-30
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "TensorFlow" , "study guide", "Machine learning" ]
---
A brief summary of what materials to study from and a list of free practice tests to prepare for the TensorFlow Developer Certificate Exam.
 
<!--more-->
 
# Introduction
 
In this guide I'll be going over what the TensorFlow Developer Certificate is, why you might want to give the exam, what you need to study to do well on the test and finally some pointers for when you're giving the test.
 
 
# What is it?
 
The TensorFlow Developer Certificate is an exam that tests your proficiency in solving machine learning and deep learning problems using TensorFlow in python. 
 
This is a coding based exam and the certificate you receive will be valid for 3 years from the date of issue. Apart from the certificate, your name will also be added to the [TensorFlow Certificate Network](https://www.tensorflow.org/certificate-network), where you can see other developers who have cleared the test. You can also search for specific developers from this page, or narrow it down by location.
 
 
# Why should I get this certification?
 
The certification is basically a sign to potential employers or clients that you are able to formulate and solve ML or DL problems with TensorFlow. If you're either looking to get into Artificial intelligence or switch roles to a ML engineer this might be a worthwhile endeavor to pursue.
 
However there are a few things to keep in mind before you decide to pursue this certification. 
- This is a relatively new certification, at the time I'm writing this, and as such it's importance in the industry might not be very high yet.
- It's not cheap. At 100 USD it's fairly expensive and might be inaccessible to a lot of people. There might be more cost effective ways to display your competence in TensorFlow.
- This certification does not test Machine learning and deep learning fundamentals, only your competence in implementing these concepts using TensorFlow.
 
If you're okay with the caveats I've listed above and you want to build your profile, then it's a great certification to have. I personally wrote the exam because I'm always looking for a new challenge and I'm passionate about Machine learning and its applications. Also this one of the only machine learning specific professional certifications around, and if you're going to be using TensorFlow might as well get your certification from the people that created it.
 
# How should I prepare?
 
The TensorFlow Developer Certificate tests your ability to use TensorFlow to solve a subset of machine learning problems. For the extensive list of topics check the [handbook](https://www.tensorflow.org/site-assets/downloads/marketing/cert/TF_Certificate_Candidate_Handbook.pdf)
 
To summarise, there are 4 main topics covered in the exam:
 
1. Tensorflow basics
2. Image classification
3. Natural language processing
4. Time series and sequences
 
Now depending on how familiar you are with Neural networks and deep learning your approach to the test will vary. For both cases you'll need a healthy dose of reading mixed with coding. Ideally you want to be implementing as many concepts you read about using code as possible.
 
## 1. Already familiar with deep learning concepts
 
Start with the DeepLearning.AI TensorFlow Developer specialization on Coursera. The course is taught by Laurence Moroney and covers basically all the details you'll need for the exam over the 4 course specialization
 
Alternatively you can use Intro to TensorFlow for Deep Learning on Udemy which is free and has similar course content.
 
A useful textbook to refer to for this is Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow, 2nd Edition. The second edition is important because it is updated for tensorflow 2.x. Only the second half of the book is useful, i.e, Part II.
 
You can find an early release of the 2nd Edition of the textbook [here](https://www.knowledgeisle.com/wp-content/uploads/2019/12/2-Aur%C3%A9lien-G%C3%A9ron-Hands-On-Machine-Learning-with-Scikit-Learn-Keras-and-Tensorflow_-Concepts-Tools-and-Techniques-to-Build-Intelligent-Systems-O%E2%80%99Reilly-Media-2019.pdf)
 
 
## 2. Complete beginner to deep learning
 
If you are a complete novice to this field I'd recommend you start off with the fundamentals and mathematics behind deep learning before diving into TensorFlow. A good place to begin is the Deep Learning Specialization from deeplearning.ai on Coursera.
 
Another great resource is the Introduction to Deep Learning course from MIT, which you can find at http://introtodeeplearning.com/. Keep in mind that only the first few courses are required for the topics tested in this exam.
 
After this you can go through the materials I've mentioned in #1
 
 
# Test Environment
 
The test has to be taken on the PyCharm IDE and will require a specific version of Python to be installed. If you're new to PyCharm like I was, I'd recommend you spend some time getting used to the layout of the editor. You can watch the videos at https://www.jetbrains.com/pycharm/learning-center/ in order to get familiarise yourself with the layout and features.
 
For specific instructuions on how to set up the environment check here https://www.tensorflow.org/extras/cert/Setting_Up_TF_Developer_Certificate_Exam.pdf
 
 
# Test day pointers
 
The test is 5 hours long and cannot be paused once it's been started. I cannot give away exactly what's asked in the exam because that would be cheating, but I can give some tips.
 
The exam can be comfortably finished in around 3 hours, this is how long it took me. Your test experience may vary based on how familiar you are with the IDE, Python and TensorFlow. After each question, you will have to submit your model for scoring and you need to be connected to the internet for this. The scores are out of 5 for each and not all questions have the same weightage, harder questions are given higher priority. You will be able to see how much you scored out of 5 once you submit the model.
 
Most of the models can be trained within the allotted time using a fairly modern CPU, if training is taking too long, you can always just train the model in Colab, and then download the model for submission.
 
# Conclusion
 
If you go through the materials I've listed above and practice coding the concepts regularly you should be able to sail through this test. I've purposely not mentioned a time frame for preparation because it'll vary wildly based on your learning ability and previous experience in this field.
 
Good luck and happy testing!
 