%# Malaria-IBM-drug-resistance
%%Here I created IBM for malaria using MATLAB
% Malaria Individual-Based Model Focused on Drug Resistance
% This script models the spread of malaria with a focus on drug resistance.
% Assume a population with susceptible, infected, and resistant individuals.

% Parameters
numIndividuals = 1000; % Total number of individuals
numTimeSteps = 365; % Simulation time in days
infectionRate = 0.1; % Rate of infection per contact
recoveryRate = 0.05; % Rate of recovery from malaria
drugResistanceRate = 0.02; % Rate at which drug resistance occurs
initialInfected = 10; % Initial number of infected individuals
initialResistant = 0; % Initial number of drug-resistant individuals

% State Variables
% 1: Susceptible, 2: Infected, 3: Resistant
population = zeros(numIndividuals, 1);
population(1:initialInfected) = 2; % Initial infected individuals

% Initialize resistant individuals
resistantIndividuals = find(population == 2);
if ~isempty(resistantIndividuals)
    resistantIndividuals = resistantIndividuals(randperm(length(resistantIndividuals), initialResistant));
    population(resistantIndividuals) = 3;
end

% Track results over time
susceptibleCount = zeros(numTimeSteps, 1);
infectedCount = zeros(numTimeSteps, 1);
resistantCount = zeros(numTimeSteps, 1);

for t = 1:numTimeSteps
    % Update counts
    susceptibleCount(t) = sum(population == 1);
    infectedCount(t) = sum(population == 2);
    resistantCount(t) = sum(population == 3);
    
    % Transition of individuals
    for i = 1:numIndividuals
        if population(i) == 2 % Infected
            if rand < recoveryRate
                if rand < drugResistanceRate
                    population(i) = 3; % Develop resistance
                else
                    population(i) = 1; % Recover to susceptible
                end
            end
        elseif population(i) == 1 % Susceptible
            % Check for new infections
            potentialInfected = find(population == 2);
            if ~isempty(potentialInfected)
                contactIndividual = potentialInfected(randi(length(potentialInfected)));
                if rand < infectionRate
                    population(i) = 2; % Get infected
                end
            end
        end
    end
end

% Plot results
figure;
plot(1:numTimeSteps, susceptibleCount, 'b', 'DisplayName', 'Susceptible');
hold on;
plot(1:numTimeSteps, infectedCount, 'r', 'DisplayName', 'Infected');
plot(1:numTimeSteps, resistantCount, 'g', 'DisplayName', 'Resistant');
xlabel('Time (days)');
ylabel('Number of Individuals');
title('Malaria Individual-Based Model with Drug Resistance');
legend;
grid on;

