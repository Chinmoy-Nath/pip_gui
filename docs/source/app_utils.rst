GUI extensions of pip
=====================

pip_tkinter.utils.runpip_using_subprocess
-----------------------------------------

A utility method to run `pip` console commands and return output. This method
uses subprocess module to run a console command and when the process execution
terminates, it returns the console output and errors.

pip_tkinter.utils.RunpipSubprocess
----------------------------------

A utility class to run `pip` commands and return *real-time* output. Since,
tkinter is not thread safe, in order to get real time output from a process
either multithreading or multiprocessing ( basically asynchronous I/O) is
needed. In both cases, output of process can be sent through a shared queue
which may be shared between multiple threads or multiple processes. However,
for this application multiprocessing turns out be a better option than
multithreading, due to following reasons :

1.  Python `multithreading` module doesn't take advantage of multiple CPU cores
    available which can degrade user expereince in case of GUI applications.
    Whereas `multiprocessing` takes advantage of multiple available cores.

2.  `multithreading` module was found to have issues with pip.main(). For more
    information about the issue click `here <https://github.com/pypa/pip/issues/2553>`_

But, tkinter widgets can't be updated from another thread or process
except the main thread or process. Therefore, possible solutions to this
problem are :

1.  Re-implement `mainloop()` method of tkinter in a thread-safe way. The
    `mainloop()` method is basically an endless loop responsible for
    periodically checking for runtime updates made to GUI and rendering them
    on the screen. A few thread-safe implementations are available like:

    -   `mtTkinter <http://tkinter.unpythonic.net/wiki/mtTkinter>`_

2.  Tkinter is designed to run from the main thread, only. See the docs:

        ``Just run all UI code in the main thread, and let the writers write to a
        Queue object.``

    The reason for tkinter to not be thread safe is that many objects and
    subsystems don't like receiving requests from multiple various threads,
    and in the case of GUI toolkit it's not rare to need specifically to use
    the main thread only.

Generally, the right Python architecture for this problem is to devote a
process (the main one, if one must) to serving the finicky object or
subsystem; every other process requiring interaction with said subsystem or
object must then obtain it by queueing requests to the dedicated process
(and possibly waiting on a "return queue" for results, if results are required as a
consequence of some request).

Therefore, `multiprocessing` module was chosen to log real time output from
running process to tkinter text widget.::

    class RunpipSubprocess():
        """
        Run pip with argument string containing command and options. Uses
        subprocess module for executing pip commands. Logs real time output in
        output queue.

        In output queue a tuple object is sent consisting of two elements:

        -   Output code : For identification of purpose of message

            Code    Purpose
            0       Start marker for logging output to tkinter text widget
            1       Output message
            2       Error message
            3       End marker for logging output to tkinter text widget

        -   Message : a string variable containing the string collected from
            stdout and stderr streams

        """

        def __init__(self, argstring, output_queue):
            """
            Initialize subprocess for running pip commands.

            :param output_queue: queue for buffering line by line output
            :param argstring: is quoted version of what would follow 'pip' on
             command line.
            """

            self.pip_process = subprocess.Popen(
                argstring.split(),
                stdout=subprocess.PIPE,
                stderr=subprocess.PIPE)
            self.output_queue = output_queue

        def start_logging_threads(self):
            """
            Starts logging to output and error queue
            """

            #multiplexing between stderr and stdout streams
            fileio_streams = [
                self.pip_process.stdout.fileno(),
                self.pip_process.stderr.fileno(),]
            io_iterator = select.select(fileio_streams, [], [])

            #send start string : 'process_started'
            self.output_queue.put((0,'process_started'))

            while True:

                #iterate over available file descriptors in io_iterator
                for file_descrp in io_iterator[0]:

                    #if something is there in stdout stream
                    if file_descrp == self.pip_process.stdout.fileno():
                        pipout = self.pip_process.stdout.readline()
                        print (pipout)
                        self.output_queue.put((1,pipout))

                    #else check in stderr stream
                    elif file_descrp == self.pip_process.stderr.fileno():
                        piperr = self.pip_process.stderr.readline()
                        print (piperr)
                        self.output_queue.put((2,piperr))

                #Check if process has ended, if process is not completed
                #then self.pip_process.poll() returns None
                if self.pip_process.poll() != None:
                    self.output_queue.put((3,self.pip_process.poll()))
                    print (self.pip_process.poll())
                    break


.. toctree::
    :maxdepth: 4