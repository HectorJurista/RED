# RED
 Berkeley Open Infrastructure for Network Computing (BOINC)
import React, { useState, useEffect } from 'react';

interface Task {
  id: number;
  name: string;
  status: 'idle' | 'running' | 'completed' | 'failed';
  progress: number; // 0 to 100
  dataSize: string; // e.g., "10MB", "2GB"
}

const initialTasks: Task[] = [
  { id: 1, name: 'AI Model Training', status: 'idle', progress: 0, dataSize: '50GB' },
  { id: 2, name: 'Data Analysis', status: 'running', progress: 35, dataSize: '1TB' },
  { id: 3, name: 'Simulation Run', status: 'completed', progress: 100, dataSize: '200GB' },
  { id: 4, name: 'Image Processing', status: 'failed', progress: 0, dataSize: '100MB' },
];

const colorPalette = {
  primary: 'indigo-600',
  primaryLight: 'indigo-200',
  secondary: 'gray-700',
  secondaryLight: 'gray-200',
  success: 'green-500',
  error: 'red-500',
  warning: 'yellow-500',
  info: 'blue-500',
  textPrimary: 'gray-900',
  textSecondary: 'gray-500',
  border: 'gray-300',
  background: 'gray-100',
};

const App = () => {
  const [tasks, setTasks] = useState<Task[]>(initialTasks);
  const [newTaskName, setNewTaskName] = useState('');
  const [newTaskDataSize, setNewTaskDataSize] = useState('');

  useEffect(() => {
    // Simulate task progress updates (replace with actual BOINC integration)
    const interval = setInterval(() => {
      setTasks((prevTasks) =>
        prevTasks.map((task) => {
          if (task.status === 'running' && task.progress < 100) {
            const newProgress = Math.min(task.progress + Math.random() * 15, 100);
            return { ...task, progress: newProgress };
          }
          return task;
        })
      );
    }, 5000); // Update every 5 seconds

    return () => clearInterval(interval);
  }, []);

  const handleAddTask = () => {
    if (newTaskName.trim() !== '' && newTaskDataSize.trim() !== '') {
      const newTask: Task = {
        id: tasks.length + 1,
        name: newTaskName,
        status: 'idle',
        progress: 0,
        dataSize: newTaskDataSize,
      };
      setTasks([...tasks, newTask]);
      setNewTaskName('');
      setNewTaskDataSize('');
    }
  };

  const handleStartTask = (id: number) => {
    setTasks((prevTasks) =>
      prevTasks.map((task) => (task.id === id ? { ...task, status: 'running' } : task))
    );
  };

  const handleCancelTask = (id: number) => {
    setTasks((prevTasks) =>
      prevTasks.map((task) => (task.id === id ? { ...task, status: 'idle' } : task))
    );
  };

  const getStatusColor = (status: string) => {
    switch (status) {
      case 'idle':
        return colorPalette.secondaryLight;
      case 'running':
        return colorPalette.info;
      case 'completed':
        return colorPalette.success;
      case 'failed':
        return colorPalette.error;
      default:
        return colorPalette.secondary;
    }
  };

  return (
    <div className="bg-gray-50 min-h-screen py-10">
      <div className="container mx-auto px-4">
        <h1 className={`text-3xl font-semibold text-${colorPalette.textPrimary} mb-6`}>
          BOINC Distributed Computing Dashboard
        </h1>

        {/* Add Task Form */}
        <div className="bg-white rounded-xl shadow-md p-6 mb-8 border border-gray-200">
          <h2 className={`text-xl font-semibold text-${colorPalette.textPrimary} mb-4`}>Add New Task</h2>
          <div className="mb-4">
            <label htmlFor="taskName" className={`block text-${colorPalette.textSecondary} text-sm font-bold mb-2`}>
              Task Name:
            </label>
            <input
              type="text"
              id="taskName"
              className={`shadow appearance-none border rounded w-full py-2 px-3 text-${colorPalette.textPrimary} leading-tight focus:outline-none focus:shadow-outline border-${colorPalette.border}`}
              value={newTaskName}
              onChange={(e) => setNewTaskName(e.target.value)}
            />
          </div>
          <div className="mb-4">
            <label htmlFor="dataSize" className={`block text-${colorPalette.textSecondary} text-sm font-bold mb-2`}>
              Data Size:
            </label>
            <input
              type="text"
              id="dataSize"
              className={`shadow appearance-none border rounded w-full py-2 px-3 text-${colorPalette.textPrimary} leading-tight focus:outline-none focus:shadow-outline border-${colorPalette.border}`}
              value={newTaskDataSize}
              onChange={(e) => setNewTaskDataSize(e.target.value)}
            />
          </div>
          <button
            className={`bg-${colorPalette.primary} hover:bg-${colorPalette.primaryLight} text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline`}
            onClick={handleAddTask}
          >
            Add Task
          </button>
        </div>

        {/* Task List */}
        <div className="bg-white rounded-xl shadow-md p-6 border border-gray-200">
          <h2 className={`text-xl font-semibold text-${colorPalette.textPrimary} mb-4`}>Current Tasks</h2>
          {tasks.length === 0 ? (
            <p className={`text-${colorPalette.textSecondary}`}>No tasks available.</p>
          ) : (
            <div className="overflow-x-auto">
              <table className="min-w-full divide-y divide-gray-200">
                <thead className="bg-gray-50">
                  <tr>
                    <th scope="col" className={`px-6 py-3 text-left text-xs font-medium text-${colorPalette.textSecondary} uppercase tracking-wider`}>
                      Task Name
                    </th>
                    <th scope="col" className={`px-6 py-3 text-left text-xs font-medium text-${colorPalette.textSecondary} uppercase tracking-wider`}>
                      Status
                    </th>
                    <th scope="col" className={`px-6 py-3 text-left text-xs font-medium text-${colorPalette.textSecondary} uppercase tracking-wider`}>
                      Progress
                    </th>
                    <th scope="col" className={`px-6 py-3 text-left text-xs font-medium text-${colorPalette.textSecondary} uppercase tracking-wider`}>
                      Data Size
                    </th>
                    <th scope="col" className="relative px-6 py-3">
                      <span className="sr-only">Actions</span>
                    </th>
                  </tr>
                </thead>
                <tbody className="bg-white divide-y divide-gray-200">
                  {tasks.map((task) => (
                    <tr key={task.id}>
                      <td className={`px-6 py-4 whitespace-nowrap text-sm font-medium text-${colorPalette.textPrimary}`}>{task.name}</td>
                      <td className="px-6 py-4 whitespace-nowrap text-sm">
                        <span className={`px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-${getStatusColor(task.status)} text-white`}>
                          {task.status}
                        </span>
                      </td>
                      <td className="px-6 py-4 whitespace-nowrap text-sm">
                        <div className="flex items-center">
                          <div className="w-24 bg-gray-200 rounded-full h-2">
                            <div className={`bg-${colorPalette.primary} h-2 rounded-full`} style={{ width: `${task.progress}%` }}></div>
                          </div>
                          <span className={`ml-2 text-xs text-${colorPalette.textSecondary}`}>{task.progress}%</span>
                        </div>
                      </td>
                      <td className={`px-6 py-4 whitespace-nowrap text-sm text-${colorPalette.textSecondary}`}>{task.dataSize}</td>
                      <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                        {task.status === 'idle' && (
                          <button
                            className={`text-${colorPalette.primary} hover:text-${colorPalette.primaryLight}`}
                            onClick={() => handleStartTask(task.id)}
                          >
                            Start
                          </button>
                        )}
                        {task.status === 'running' && (
                          <button
                            className={`text-${colorPalette.error} hover:text-${colorPalette.error}`}
                            onClick={() => handleCancelTask(task.id)}
                          >
                            Cancel
                          </button>
                        )}
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          )}
        </div>
      </div>
    </div>
  );
};

export default App;
