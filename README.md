# RTOS-TASK-SCHEDULER
-------------------code--------------------------------- 

#include <iostream>
#include <vector>
#include <algorithm>
#include <iomanip>

using namespace std;

enum State
{
    READY,
    RUNNING,
    FINISHED
};

struct Task
{
    int id;
    string name;
    int priority;
    int burstTime;
    int remainingTime;
    State state;
};

class RTOSScheduler
{
private:
    vector<Task> tasks;
    int tick = 0;

public:

    void addTask(int id, string name, int priority, int burst)
    {
        Task t;
        t.id = id;
        t.name = name;
        t.priority = priority;
        t.burstTime = burst;
        t.remainingTime = burst;
        t.state = READY;

        tasks.push_back(t);
    }

    void displayTasks()
    {
        cout << "\n-------------------------------------------------------------\n";
        cout << left << setw(10) << "Task"
             << setw(10) << "Priority"
             << setw(10) << "Burst"
             << setw(12) << "Remaining"
             << setw(12) << "State" << endl;

        cout << "-------------------------------------------------------------\n";

        for(auto &t : tasks)
        {
            string s;

            if(t.state == READY)
                s = "READY";
            else if(t.state == RUNNING)
                s = "RUNNING";
            else
                s = "FINISHED";

            cout << left
                 << setw(10) << t.name
                 << setw(10) << t.priority
                 << setw(10) << t.burstTime
                 << setw(12) << t.remainingTime
                 << setw(12) << s
                 << endl;
        }
    }

    void schedule()
    {
        cout << "\n=========== RTOS Scheduler Started ===========\n";

        while(true)
        {
            int selected = -1;

            // Select Highest Priority READY Task
            for(int i=0;i<tasks.size();i++)
            {
                if(tasks[i].state == READY)
                {
                    if(selected==-1 ||
                       tasks[i].priority > tasks[selected].priority)
                    {
                        selected = i;
                    }
                }
            }

            if(selected==-1)
                break;

            tick++;

            cout << "\n--------------------------------";
            cout << "\nTick : " << tick;

            tasks[selected].state = RUNNING;

            cout << "\nRunning Task : "
                 << tasks[selected].name
                 << " (Priority "
                 << tasks[selected].priority
                 << ")";

            tasks[selected].remainingTime--;

            if(tasks[selected].remainingTime==0)
            {
                tasks[selected].state = FINISHED;

                cout << "\nTask Completed : "
                     << tasks[selected].name;
            }
            else
            {
                tasks[selected].state = READY;
            }

            displayTasks();
        }

        cout << "\n=========== All Tasks Finished ===========\n";
    }
};

int main()
{
    RTOSScheduler scheduler;

    scheduler.addTask(1,"Sensor",3,4);
    scheduler.addTask(2,"CAN",5,3);
    scheduler.addTask(3,"Logger",2,5);
    scheduler.addTask(4,"Display",1,2);

    scheduler.displayTasks();

    scheduler.schedule();

    return 0;
}
