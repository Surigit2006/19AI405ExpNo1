<h1>ExpNo 1 :Developing AI Agent with PEAS Description</h1>
<h3>Name: Saravanan N</h3>
<h3>Register Number/Staff Id: TSML006</h3>


<h3>AIM:</h3>
<br>
<p>To find the PEAS description for the given AI problem and develop an AI agent.</p>
<br>
<h3>Theory</h3>
<h3>Medicine prescribing agent:</h3>
<p>Such this agent prescribes medicine for fever (greater than 98.5 degrees) which we consider here as unhealthy, by the user temperature input, and another environment is rooms in the hospital (two rooms). This agent has to consider two factors one is room location and an unhealthy patient in a random room, the agent has to move from one room to another to check and treat the unhealthy person. The performance of the agent is calculated by incrementing performance and each time after treating in one room again it has to check another room so that the movement causes the agent to reduce its performance. Hence, agents prescribe medicine to unhealthy.</p>
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
    <td><strong>Medicine prescribing agent</strong></td>
    <td><strong>Treating unhealthy, agent movement</strong></td>
     <td><strong>Rooms, Patient</strong></td>
    <td><strong>Medicine, Treatment</strong></td>
    <td><strong>Location, Temperature of patient</strong></td>
  </tr>
</table>
<hr>
<H3>DESIGN STEPS</H3>
<h3>STEP 1:Identifying the input:</h3>
<p>Temperature from patients, Location.</p>
<h3>STEP 2:Identifying the output:</h3>
<p>Prescribe medicine if the patient in a random has a fever.</p>
<h3>STEP 3:Developing the PEAS description:</h3>
<p>PEAS description is developed by the performance, environment, actuators, and sensors in an agent.</p>
<h3>STEP 4:Implementing the AI agent:</h3>
<p>Treat unhealthy patients in each room. And check for the unhealthy patients in random room</p>
<h3>STEP 5:</h3>
<p>Measure the performance parameters: For each treatment performance incremented, for each movement performance decremented</p>
<H4>Program</H4>

```
import random

FEVER_THRESHOLD = 98.5

class HospitalEnv:
    def __init__(self, temp_A=None, temp_B=None, start_room=None, seed=None):
        if seed is not None:
            random.seed(seed)
        # Initialize patient temperatures in two rooms (A, B)
        self.temps = {
            'A': temp_A if temp_A is not None else round(random.uniform(97.0, 103.0), 1),
            'B': temp_B if temp_B is not None else round(random.uniform(97.0, 103.0), 1)
        }
        # Agent starts in A or B
        self.agent_room = start_room if start_room in ('A', 'B') else random.choice(['A', 'B'])
        # Performance: +1 for each treatment, -1 for each move
        self.performance = 0
        # History of actions and observations
        self.log = []

    def sense(self):
        # Sensors: current room and patient temperature in that room
        room = self.agent_room
        temp = self.temps[room]
        return room, temp

    def actuate_treat(self):
        room = self.agent_room
        # If fever detected, treat and normalize temperature
        if self.temps[room] > FEVER_THRESHOLD:
            self.temps[room] = FEVER_THRESHOLD  # mark as non-febrile post-treatment
            self.performance += 1  # reward for treatment
            self.log.append(f"TREAT in {room}: performance={self.performance}")
        else:
            # No fever: no reward; still record a no-op treatment
            self.log.append(f"TREAT (no fever) in {room}: performance={self.performance}")

    def actuate_move(self):
        # Move to the other room with a penalty
        self.agent_room = 'B' if self.agent_room == 'A' else 'A'
        self.performance -= 1
        self.log.append(f"MOVE to {self.agent_room}: performance={self.performance}")

    def both_rooms_non_febrile(self):
        return self.temps['A'] <= FEVER_THRESHOLD and self.temps['B'] <= FEVER_THRESHOLD

class MedicinePrescribingAgent:
    def __init__(self):
        pass

    def policy(self, percept):
        # Simple reflex policy: if fever in current room -> treat; else move
        room, temp = percept
        if temp > FEVER_THRESHOLD:
            return "TREAT"
        else:
            return "MOVE"

def run_episode(env: HospitalEnv, agent: MedicinePrescribingAgent, max_steps=10):
    for step in range(max_steps):
        if env.both_rooms_non_febrile():
            env.log.append(f"STOP: both rooms non-febrile at step {step}, performance={env.performance}")
            break
        percept = env.sense()
        action = agent.policy(percept)
        if action == "TREAT":
            env.actuate_treat()
        elif action == "MOVE":
            env.actuate_move()
        else:
            env.log.append(f"UNKNOWN ACTION: {action}")
            break
    return env

if __name__ == "__main__":
    # Example runs
    # Randomized environment
    env1 = HospitalEnv(seed=42)
    agent = MedicinePrescribingAgent()
    run_episode(env1, agent, max_steps=10)
    print("Run 1 (randomized):")
    print(f"Start room: {env1.agent_room}, Final temps: {env1.temps}, Performance: {env1.performance}")
    for line in env1.log:
        print(line)

    # Deterministic environment: fever in B only, start at A
    env2 = HospitalEnv(temp_A=98.0, temp_B=101.2, start_room='A')
    run_episode(env2, agent, max_steps=10)
    print("\nRun 2 (deterministic):")
    print(f"Final temps: {env2.temps}, Performance: {env2.performance}")
    for line in env2.log:
        print(line)
```
<H5>OUTPUT</H5>
<img width="885" height="260" alt="image" src="https://github.com/user-attachments/assets/11a90ce2-9422-48e4-832f-c3c9de383dfc" />

