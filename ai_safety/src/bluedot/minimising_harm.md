# Minimising harm

## Zooming in on one threat 

A fanatic/terrorist group with biology capabilities and desire to topple current system attacks civilians by unleashing a bio-weapon in order to blackmail the institutions into accepting their demands.

##  Kill chain exercise

### Phase 1: CAPABILITY EMERGENCE

What AI capability makes this attack possible? What new AI ability makes this possible?
Required capability: Biological knowledge to enable common people the capability to build bio weapons
Current capability gap: I believe AIs has that knowledge already but it's still hard to build bio weapons
Warning signs we'd see first: Bioweapons used in current wars from non-governmental entities

### Phase 2: ACCESS & WEAPONISATION

How does the attacker gain and prepare the capability? How does the attacker get and prepare it?
Access method: Open-source model, jailbreaks on non-secure models
Preparation steps: Gain the knowledge and gain materials to do biological experiments
Time required: Months

### Phase 3: INITIAL BREACH

What's the first domino to fall? First foothold or incident.
Entry point: Creation of a pathogen
Method: Experiments succeed
Detection likelihood: Quite likely since it needs specific materials

### Phase 4: ESCALATION CASCADE

How does initial success compound? How does it spread or intensify?
Propagation mechanism: The pathogen spread by itself and attackers continue to spread
Failed safeguards: 
  - Alignment 
  - Guardrails 
  - 
Point of no return: 
  - Pathogen successfully spread

### Phase 5: SOCIETAL BREAKDOWN

What fails at the human/institutional level? What fails at the human/institutional level?
Trust collapse: Healthcare fails to fight/contain the pathogen
Coordination failure: Crisis, and no known 
Public reaction: Panic, people lock themselves

### Phase 6: CATASTROPHIC OUTCOME

Make it visceral - what do people experience? What people see, feel, and lose.
First 24 hours: [What headlines appear? What do hospitals report?]
  - New pathogen discovered, 100 cases 
First week: 
  - The pathogen was synthetically created 
  - People are hoarding groceries
  - No known cure in the following months 
  - People are asked to isolate themselves
Irreversible damage: 
  - Deaths: thousands 
  - Governments are paralyzed 
  - Economy does not recover, fanatic groups rise, people live in fear
*[Deaths: ___ / Economic loss: $*__ / What never recovers?]_

Phase 7: IDENTIFY CHOKE POINTS

The main detection window is suspicious buying of biological materials.
Maybe detect IP of people making such requests is not self-hosted

## Capabilities required for harm

> List 3-5 specific technical capabilities or behaviors your threat requires. Be concrete about what an AI would need to be able to do.
> E.g. Self-replication, goal persistence across instances, long-term planning and coordination, resource acquisition (compute, money), bioweapon knowledge/synthesis

- AI is knowledgeable about chemistry/biology
- AI is self-trained with open weights / easy to jailbreak


## Building defences

> Which capability/behaviour is the most important? If we prevented just this ONE, would the entire threat collapse?
> Reviewing what you’ve learned throughout the course, determine how we could:

The most important is sharing knowledge about biology/chemistry.
The threat would collapse.

- Prevent the model from acquiring this during training
  - data filtering
  - knowledge removal post training? with interpretability
- Detect the capability/behaviour
  - Evaluation of knowledge through set of questions
- Constrain the model so it cannot use this to take dangerous actions
  - alignment techniques
  - ai control techniques
  - all of these require all available model to enforce it

## Open source AIs
