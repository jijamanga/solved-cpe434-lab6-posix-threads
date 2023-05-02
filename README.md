Download Link: https://assignmentchef.com/product/solved-cpe434-lab6-posix-threads
<br>
A thread is a semi-process, which has its own stack, and executes a given piece of code. Unlike a real process, the thread normally shares its memory with other threads (whereas for processes we usually have a different memory area for each one of them). A Thread Group is a set of threads all executing inside the same process. They all share the same memory, and thus can access the same global variables, same heap memory, same set of file descriptors, etc. All these threads execute in parallel (i.e. using time slices, or if the system has several processors, then really in parallel).

Pthreads are defined as a set of C language programming types and procedure calls. Vendors usually provide a Pthreads implementation in the form of a header/include file and a library, which you link with your program.

<h2>Demo Codes</h2>

<h3>Demo 1</h3>

<table width="589">

 <tbody>

  <tr>

   <td width="589">#include &lt;pthread.h#include &lt;stdio.h&gt;#define NUM_THREADS 5void* ​printHello​(​void *​threadId​){printf​(​”
%d:Hello World!
”​,​threadId​);  pthread_exit​(​NULL​);}int ​main​(){pthread_t threads​[​NUM_THREADS​];  int ​rc​,​t​;for​(​t​=​0​;​t​&lt;​NUM_THREADS​;​t​++​){  printf​(​”Creating thread %d
”​,​t​);rc ​= ​pthread_create​(​&amp;​threads​[​t​],​NULL​,​printHello​,(​void*​)​t​);  if​(​rc​){printf​(​”ERROR:Return Code from pthread_create() is %d
”​,​rc​);}}pthread_exit​(​NULL​);}</td>

  </tr>

 </tbody>

</table>

You can also define a global variable such that it is thread-specific. The variable is global, but the value it holds will be thread specific. In the snippet below, the modifier ​<strong>__thread ​</strong>defines the variable ​<em>myData ​</em>as ​<strong>Thread Local Storage​</strong>.




<table width="591">

 <tbody>

  <tr>

   <td width="591">#include statements  __thread int ​     myData​  ;​  void *threadFunc​            ()​{//access and modification of data “myData” will be local}int main​ ()​{//create and launch threads//join threads}</td>

  </tr>

 </tbody>

</table>

There is another way to achieve the same thing. Here you can use ​<strong>pthread_key_t ​</strong>as the modifier.




<table width="591">

 <tbody>

  <tr>

   <td width="591">#include statements  pthread_key_t myKey​;void* ​threadFunc​(){int* ​data ​= ​malloc​(​sizeof​(​int​));//this modification is thread specific  pthread_setspecific​(​myKey​,​data​);//access the data using following// the real use for this is when you need to access it from another function, but want it to be specific to a thread  int* ​myLoc ​= ​pthread_getspecific​(​myKey​);}  int ​main​(){pthread_key_create​(​&amp;​myKey​,​NULL​);//create and launch threads//join threads}</td>

  </tr>

 </tbody>

</table>







<strong>             </strong>

<h2>Demo 2</h2>

<table width="589">

 <tbody>

  <tr>

   <td width="589">/*Written By: Prawar Poudel13 Feb 2018This is written to demonstrate simple creation and waiting for pthread to terminate*/#include &lt;pthread.h&gt;#include &lt;stdio.h&gt;#include &lt;unistd.h&gt;#define NUM_THREADS 5//the argument that will be sent will be the (int) id  void *​simpleThreadFunc​(​void* ​argument​){int ​myId ​= ​(​int​)​argument​;  printf​(​”My Id is %d
”​,​myId​);int ​a ​= ​0​;}int ​main​(){//you can create these dynamically also  pthread_t myThreads​[​NUM_THREADS​];int ​status ​= ​0​;for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){printf​(​”Creating thread no. %d, and sending ID %d
”​,​i​,​i​);  status ​= ​pthread_create​(​&amp;​myThreads​[​i​],​NULL​,​simpleThreadFunc​,(​void*​)​i​); ​if​(​status​){printf​(​”Error in creating the threads: %d
”​,​i​);  return ​-​1​;}​else{printf​(​”Successful creation of thread..
”​);}}//this is the area that threads will run//we will wait for the threads here  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){  int ​retStatus ​= ​pthread_join​(​myThreads​[​i​],​NULL​);  if​(​!​retStatus​){printf​(​”Successful termination of thread id %d
”​,​i​);  }​elseprintf​(​”Well….. some problem at thread id %d, error no:  %d
”​,​i​,​retStatus​);}return ​0​;</td>

  </tr>

 </tbody>

</table>

}

<strong> </strong>




<h2>Demo 3</h2>

<table width="589">

 <tbody>

  <tr>

   <td width="589">/*Written By: Prawar Poudel13 Feb 2018This is written to demonstrate simple creation and waiting for pthread to terminate*/#include &lt;pthread.h&gt;#include &lt;stdio.h&gt;#include &lt;unistd.h&gt;#define NUM_THREADS 100int ​mutexProtectedGlobalVariable​;  int ​unprotectedProtectedGlobalVariable​;  pthread_mutex_t myMutex​;//this function will update the value without any protection ​void *​unprotectedThreadFunc​(​void* ​argument​){for​(​int ​i​=​0​;​i​&lt;​10000​;​i​++​) ​unprotectedProtectedGlobalVariable​++​; }//this function will update the value without any protection ​void *​protectedThreadFunc​(​void* ​argument​){pthread_mutex_lock ​(​&amp;​myMutex​);for​(​int ​i​=​0​;​i​&lt;​10000​;​i​++​) ​mutexProtectedGlobalVariable​++​; pthread_mutex_unlock ​(​&amp;​myMutex​);}int ​main​(){mutexProtectedGlobalVariable ​= ​0​;  unprotectedProtectedGlobalVariable ​= ​0​;//you can create these dynamically also  pthread_t myThreads​[​NUM_THREADS​];int ​status ​= ​0​;printf​(​”Calling unprotected set of threads
”​);//first set of five threads will call a function that will update the variable unprotected  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){status ​= ​pthread_create​(​&amp;​myThreads​[​i​],​NULL​,​unprotectedThreadFunc​, (​void*​)​i​);  if​(​status​){printf​(​”Error in creating the threads: %d
”​,​i​);  return ​-​1​;</td>

  </tr>

  <tr>

   <td width="589">}}//this is the area that threads will run//we will wait for the threads here  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){  int ​retStatus ​= ​pthread_join​(​myThreads​[​i​],​NULL​);  if​(​retStatus​){printf​(​”Well….. some problem at thread id %d, error no: %d
”​,i​ ​,retStatus​ ​);}}printf​(​”Unprotected sum is %d
”​, ​unprotectedProtectedGlobalVariable​); ​printf​(“tt…end of​   unprotected set of threads
”​);printf​(​”Calling protected set of threads
”​);  pthread_mutex_init​(​&amp;​myMutex​, ​NULL​);//next set of five threads will call a function that will update the variable protected  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){ status ​= ​pthread_create​(​&amp;​myThreads​[​i​],​NULL​,​protectedThreadFunc​, (​void*​)​i​);  if​(​status​){printf​(​”Error in creating the threads: %d
”​,​i​);  return ​-​1​;}}//this is the area that threads will run//we will wait for the threads here  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){  int ​retStatus ​= ​pthread_join​(​myThreads​[​i​],​NULL​);  if​(​retStatus​)  {printf​(​”Well….. some problem at thread id %d, error no:  %d
”​,​i​,​retStatus​);}}pthread_mutex_destroy​(​&amp;​myMutex​);  printf​(​”Protected sum is %d 
”​, ​mutexProtectedGlobalVariable​); ​printf​(​”tt…end of unprotected set of threads
”​);return ​0​;}</td>

  </tr>

 </tbody>

</table>

<strong> </strong>

<strong>             </strong>

<h2>Demo 4</h2>

<table width="589">

 <tbody>

  <tr>

   <td width="589">/*Written By: Prawar Poudel13 Feb 2018This is written to demonstrate the usage of Thread Local Storage Here using identifier __thread , we have made myVal and myArr[] thread local */#include &lt;pthread.h&gt;#include &lt;stdio.h&gt;#include &lt;unistd.h&gt;#define NUM_THREADS 5  #define ARRSIZE 5//this value is a global variable,// but we will store it as thread local, meaning while it is still global the value will can be modified such that the modified value is thread specific ​__thread ​int ​myVal​;__thread ​int ​myArr​[​ARRSIZE​];void ​printMyVal​(​int ​id​){printf​(​”My value myVal from thread %d is %d
”​, ​id​,​myVal​);  printf​(​”My arr value are
”​);  for​(​int ​i​=​0​;​i​&lt;​ARRSIZE​;​i​++​){printf​(​”%dthe element is %d
”​,​i​,​myArr​[​i​]);} }//the argument that will be sent will be the (int) id  void *​simpleThreadFunc​(​void* ​argument​){int ​myId ​= ​(​int​)​argument​;  printf​(​”My Id is %d
”​,​myId​);//just setting some thread specific value to the variable  myVal ​= ​myId​*​100​;for​(​int ​i​=​0​;​i​&lt;​5​;​i​++​)myArr​[​i​] ​= ​(​myId​*​100​+​i​);printMyVal​(​myId​);}int ​main​(){//you can create these dynamically also  pthread_t myThreads​[​NUM_THREADS​];int ​status ​= ​0​;for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){printf​(​”Creating thread no. %d, and sending ID %d
”​,​i​,​i​);  status ​= ​pthread_create​(​&amp;​myThreads​[​i​],​NULL​,​simpleThreadFunc​,(​void*​)​i​); ​if​(​status​){printf​(​”Error in creating the threads: %d
”​,​i​);  return ​-​1​;</td>

  </tr>

  <tr>

   <td width="589">}​else{printf​(​”Successful creation of thread..
”​);}}//this is the area that threads will run//we will wait for the threads here  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){  int ​retStatus ​= ​pthread_join​(​myThreads​[​i​],​NULL​);  if​(​!​retStatus​){printf​(​”Successful termination of thread id %d
”​,​i​);  }​elseprintf​(​”Well….. some problem at thread id %d, error no:  %d
”​,​i​,​retStatus​);}return ​0​;}</td>

  </tr>

 </tbody>

</table>

<strong> </strong>

<h2>Demo 5</h2>

<strong> </strong>

<table width="589">

 <tbody>

  <tr>

   <td width="589">/*Written By: Prawar Poudel13 Feb 2018This is written to demonstrate the usage of Thread Local Storage using key */#include &lt;pthread.h&gt;#include &lt;stdio.h&gt;#include &lt;unistd.h&gt;#define NUM_THREADS 5#define ARRSIZE 5//in this program we will use a key defined by pthread_key_t to define a key ​pthread_key_t myKey​;void ​printMyVal​(​int ​myId​){printf​(​”Getting specific value for thread %d using key
”​,​myId​); ​int *​myVal ​= pthread_getspecific​(​myKey​);printf​(​”t..The thread local value in thread id %d is %d
”​,​myId​,​*​myVal​); }//the argument that will be sent will be the (int) id  void *​simpleThreadFunc​(​void* ​argument​){int ​myId ​= ​(​int​)​argument​;</td>

  </tr>

  <tr>

   <td width="589">//this variable will be thread specific value that we will print from other function  int ​myVal ​= ​myId​*​100​;printf​(​”Creating the variable ::%d that will be referred to by Key from threadid%d
”​,​myVal​,​myId​);if​(​!​pthread_setspecific​(​myKey​,(​void*​)​&amp;​myVal​))  {}​else{printf​(​”Erorr in setting specific key in thread id %d
”​,​myId​); }printMyVal​(​myId​);}int ​main​(){//you can create these dynamically also  pthread_t myThreads​[​NUM_THREADS​];  int ​status ​= ​0​;pthread_key_create​(​&amp;​myKey​,​NULL​);for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){printf​(​”Creating thread no. %d, and sending ID %d
”​,​i​,​i​);  status ​= ​pthread_create​(​&amp;​myThreads​[​i​],​NULL​,​simpleThreadFunc​,(​void*​)​i​); ​if​(​status​){printf​(​”Error in creating the threads: %d
”​,​i​);  return ​-​1​;}​else{printf​(​”Successful creation of thread %d..
”​,​i​);}}//this is the area that threads will run//we will wait for the threads here  for​(​int ​i​=​0​;​i​&lt;​NUM_THREADS​;​i​++​){  int ​retStatus ​= ​pthread_join​(​myThreads​[​i​],​NULL​);  if​(​!​retStatus​){printf​(​”Successful termination of thread id %d
”​,​i​);  }​elseprintf​(​”Well….. some problem at thread id %d, error no:  %d
”​,​i​,​retStatus​);}return ​0​;}<strong> </strong></td>

  </tr>

 </tbody>

</table>

Write a parallel program to compute the definite integral using Rectangular decomposition:

Write a program in a general pthreads manner, so that they can utilize an arbitrary number of threads so the computation is divided among these computational entities as evenly as possible. Design program so that the ​<strong>number of intervals and the number of operating threads ​</strong>can be entered as a ​<strong>run time parameter (ie command line argument) ​</strong>by the user under the constraint that it must be greater than or equal to the number of threads that are used. Plot the execution time taken by 100,000 intervals with 1, 2, 4, 8 and 16 threads.

You are expected to do the following tasks:

<ul>

 <li>Write a serial code version to compute integral, using a timing function to evaluate its execution time .</li>

 <li>Write a parallel version of code using pthreads model, compare the result and execution time.</li>

 <li>Compare the performance of two models (serial and pthreads ). Try different number of intervals by increasing the number. Show graph to summary the execution time of the two models versus different number of intervals (ie 1,000, 10,000 and 100,000 intervals with serial vs parallel with 2 threads only.)</li>

 <li>Compare the performance of the serial model and parallel based on the execution time. (ie. 100,000 intervals with serial vs parallel with 1, 2, 4, 8 and 16 threads)</li>

</ul>




<strong>Hint:  </strong>

You need to study the integral using rectangular decomposition, or trapezoidal method.How to measure the execution time in serial programs. Also timing functions could be used to measure the execution time in the pthread model.

<h1>Topics for Theory</h1>

<ul>

 <li>Difference between Threads and Processes</li>

 <li>Rectangular Decomposition Method</li>

 <li>Thread Local Storage</li>

 <li>Mutex and Semaphore</li>

</ul>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<h1>Deliverables</h1>

<h2>Lab Report</h2>

The following material in each section is expected:

<ol>

 <li>Cover page with your name, lab number, course name, and dates</li>

 <li>Theory/Background (Material or methods relevant to the lab, a few sentences on each in your own words)

  <ol>

   <li>Difference between Threads and Processes</li>

   <li>Rectangular Decomposition Method</li>

   <li>Thread Local Storage</li>

   <li>Mutex and Semaphore</li>

  </ol></li>

 <li>Observations

  <ol>

   <li>Comparison of performance of the two models (serial and pthreads). Example, serial vs 2 pthreads for intervals including 1,000, 10,000, and 100,000. Program output should be included here.</li>

   <li>Comparison of performance between the serial model and parallel model based on execution time. Example, serial vs parallel with 1, 2, 4, 8, and 16 threads for 100,000 iterations. Program output should be included here.</li>

  </ol></li>

 <li>Conclusion (Did your program work as expected, what can you take away from the lab?)</li>

 <li>Appendix (for source code, submit the text in a table)</li>

</ol>

The report should be submitted as a single pdf document with the source code for your program within it.