<h1>ExpNo 1 :Developing AI Agent with PEAS Description</h1>
<h3>Name: S Harish Kumar </h3>
<h3>Register Number:  212221230104 </h3>


<h3>AIM:</h3>
<br>
<p>To find the PEAS description for the given AI problem and develop an AI agent.</p>
<br>
<h3>Theory</h3>
<h3>Vaccum Cleaner agent:</h3>
<p>The VacuumCleanerAgent is a Python class that simulates the behavior of a basic vacuum cleaner in a two-location environment ("A" and "B"). The agent can perform four actions: move left, move right, suck dirt, and do nothing. Its state includes the current location and dirt status in each location. The agent's initial state is at location "A" with no dirt. Actions like moving and sucking dirt can change its state, and the print_status method displays the current location and dirt status. This agent provides a foundation for simple vacuum cleaner simulations and can be adapted for more complex scenarios.</p>
<hr>
<h3>PEAS DESCRIPTION:</h3>
<table>
  <tr>
    <td><strong>Agent Type</strong></td>
    <td><strong>Performance</strong></td>
     <td><strong>Environment</strong></td>
    <td><strong>Actuators</strong></td>
    <td><strong>Sensors</strong></td>
  </tr>
    <tr>
    <td><strong>Vaccum Cleaner agent</strong></td>
    <td><strong>Cleaning Dirt</strong></td>
     <td><strong>floors</strong></td>
    <td><strong>cleaning,sucking</strong></td>
    <td><strong>Location, Sensing Dirt</strong></td>
  </tr>
</table>
<hr>
<H3>DESIGN STEPS</H3>
<h3>STEP 1:Identifying the input:</h3>
<p> Location.</p>
<h3>STEP 2:Identifying the output:</h3>
<p>move_left: Moves the agent to the left if it is currently at location "B.". move_right: Moves the agent to the right if it is currently at location "A." suck_dirt: Sucks dirt in the current location if there is dirt present. After sucking dirt, the dirt status in that location is updated to indicate cleanliness. do_nothing: Represents a passive action where the agent remains idle.</p>
<h3>STEP 3:Developing the PEAS description:</h3>
<p>PEAS description is developed by the performance, environment, actuators, and sensors in an agent.</p>
<h3>STEP 4:Implementing the AI agent:</h3>
<p>Clean the room and Search for dirt and suck it.</p>
<h3>STEP 5:</h3>
<p>Measure the performance parameters: For each cleaning performance incremented, for each movement performance decremented</p>
<h3>CODE:</h3>

```python
import random
import time


class Thing: 
    """
    This represents any physical object that can appear in an Environment. """

    def is_alive(self):
        """Things that are 'alive' should return true."""
        return hasattr(self, "alive") and self.alive

    def show_state(self):
        """Display the agent's internal state. Subclasses should override."""
        print("I don't know how to show_state.")
class Agent(Thing):
    
    """
        An Agent is a subclass of Thing """

    def __init__(self, program=None):
        self.alive = True
        self.performance = 0 
        self.program = program

    def can_grab(self, thing):
        """Return True if this agent can grab this thing. Override for appropriate subclasses of Agent and Thing.""" 
        return False
def TableDrivenAgentProgram(table): 
    """
    [Figure 2.7]
    This agent selects an action based on the percept sequence. It is practical only for tiny domains.
    To customize it, provide as table a dictionary of all
    {percept_sequence:action} pairs. """
    percepts = []
    

    def program(percept):
        action = None
        percepts.append(percept)
        action = table.get(tuple(percepts))
        return action 
    return program
room_A, room_B = (0,0), (1,0) 
def TableDrivenVacuumCleanerAgent():
    """
    Tabular approach towards hospital function. 
    """
        
    table = {
    ((room_A, "clean"),): "Right",
    ((room_A, "dirty"),): "suck",
    ((room_B, "clean"),): "Left",
    ((room_B, "dirty"),): "suck",
    ((room_A, "dirty"), (room_A, "clean")): "Right",
    ((room_A, "clean"), (room_B, "dirty")): "suck",
    ((room_B, "clean"), (room_A, "dirty")): "suck",
    ((room_B, "dirty"), (room_B, "clean")): "Left",
    ((room_A, "dirty"), (room_A, "clean"), (room_B, "dirty")): "suck",
    ((room_B, "dirty"), (room_B, "clean"), (room_A, "dirty")): "suck",
    }
    return Agent(TableDrivenAgentProgram(table))
TableDrivenVacuumCleanerAgent()
class Environment:
    """Abstract class representing an Environment. 'Real' Environment classes inherit from this. Your Environment will typically need to implement:
    percept:	Define the percept that an agent sees. execute_action:	Define the effects of executing an action.
    Also update the agent.performance slot.
    The environment keeps a list of .things and .agents (which is a subset of .things). Each agent has a .performance slot, initialized to 0.
    Each thing has a .location slot, even though some environments may not need this."""

    def __init__(self):
        self.things = [] 
        self.agents = []
        #room_A, room_B = (0,0), (1,0) # The two locations for the Doctor to treat

    def percept(self, agent):
        """Return the percept that the agent sees at this point. (Implement this.)"""
        raise NotImplementedError

    def execute_action(self, agent, action):
        """Change the world to reflect this action. (Implement this.)""" 
        raise NotImplementedError

    def default_location(self, thing):
        """Default location to place a new thing with unspecified location."""
        return None

    def is_done(self):
        """By default, we're done when we can't find a live agent.""" 
        return not any(agent.is_alive() for agent in self.agents)

    def step(self):
        """Run the environment for one time step. If the
        actions and exogenous changes are independent, this method will do. If there are interactions between them, you'll need to override this method."""
        if not self.is_done(): 
            actions = []
            for agent in self.agents:
                if agent.alive:
                    actions.append(agent.program(self.percept(agent))) 
                else:
                    actions.append("")
            for (agent, action) in zip(self.agents, actions): 
                self.execute_action(agent, action)

    def run(self, steps=1000):
        """Run the Environment for given number of time steps."""
        for step in range(steps):
            if self.is_done():
                return 
            self.step()

    def add_thing(self, thing, location=None):
        """Add a thing to the environment, setting its location. For convenience, if thing is an agent program we make a new agent for it. (Shouldn't need to override this.)"""
        if not isinstance(thing, Thing):
            thing = Agent(thing)
        if thing in self.things:
            print("Can't add the same thing twice") 
        else:
            thing.location = (location if location is not None else self.default_location(thing))
            self.things.append(thing) 
            if isinstance(thing, Agent):
                thing.performance = 0 
                self.agents.append(thing)

    def delete_thing(self, thing):
        """Remove a thing from the environment."""
        try:
            
            self.things.remove(thing) 
        except ValueError as e:
            print(e)
            print(" in Environment delete_thing")
            print(" Thing to be removed: {} at {}".format(thing, thing.location))
            print(" from list: {}".format([(thing, thing.location) for thing in self.things]))
        if thing in self.agents: 
            self.agents.remove(thing)
class TrivialIndoorEnvironment(Environment):
    """This environment has two locations, A and B. Each can be dirty or clean. The agent perceives its location and the location's status. This serves as an example of how to implement a simple Environment."""

    def __init__(self):
        super().__init__()
        #room_A, room_B = (0,0), (1,0) # The two locations for the Doctor to suck
        self.status = {room_A: random.choice(["clean", "dirty"]), room_B: random.choice(["clean", "dirty"]),}

    def thing_classes(self):
        return [TableDrivenVacuumCleanerAgent]

    def percept(self, agent):
        """Returns the agent's location, and the location status (dirty/clean)."""
        return agent.location, self.status[agent.location]

    def execute_action(self, agent, action):
        """Change agent's location and/or location's status; track performance. Score 10 for each treatment; -1 for each move."""
        if action == "Right":
            agent.location = room_B
            agent.performance -= 1
        elif action == "Left":
            agent.location = room_A
            agent.performance -= 1
        elif action == "suck":
            tem=float(input("performance: ")) 
            if tem>=98.5:
                self.status[agent.location] == "dirty"
                print("medicine prescribed: paracetamol and anti-biotic(low dose)")
                agent.performance += 10
            else:
                self.status[agent.location] = "clean" 
            self.status[agent.location] = "clean"


    def default_location(self, thing):
           
        return random.choice([room_A, room_B])

if   __name__ == "__main__":
    
    agent = TableDrivenVacuumCleanerAgent() 
    environment = TrivialIndoorEnvironment() 
    #print(environment)
    environment.add_thing(agent)
    print("\tStatus of vacuum cleaner before cleaning")
    print(environment.status)
    print("AgentLocation : {0}".format(agent.location)) 
    print("Performance : {0}".format(agent.performance))
    time.sleep(3)
    for i in range(2):
        environment.run(steps=1)
        print("\n\tStatus of vacuum cleaner after the treatment") 
        print(environment.status)
        print("AgentLocation : {0}".format(agent.location)) 
        print("Performance : {0}".format(agent.performance)) 
        time.sleep(3)

```
<h3>OUTPUT:</h3>

![image](https://github.com/Adithya-Siddam/19AI405ExpNo1/assets/93427248/969b6190-1cf2-4296-9585-a7dec5b45992)

<h3>RESULT:</h3>
Thus, an AI agent is developed with PEAS description.
