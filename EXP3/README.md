## CODE
```
clear;
clc;
close all;

Fs = 5000;
T = 1/Fs;
fc = 1000;
Omega_c = 2*pi*fc;

% Analog low-pass filter
b_a = Omega_c;
a_a = [1 Omega_c];

% Impulse Invariance Method
[b_imp, a_imp] = impinvar(b_a, a_a, Fs);

% Bilinear Transformation with Prewarping
Omega_p = (2/T)*tan(pi*fc/Fs);

b_a_pre = Omega_p;
a_a_pre = [1 Omega_p];

[b_blt, a_blt] = bilinear(b_a_pre, a_a_pre, Fs);

% Display coefficients
fprintf('EXPERIMENT 3: IMPULSE INVARIANCE VS BILINEAR TRANSFORMATION\n\n');

fprintf('Analog Cutoff Frequency = %.2f rad/s\n', Omega_c);
fprintf('Prewarped Frequency = %.2f rad/s\n\n', Omega_p);

disp('Impulse Invariance Numerator:');
disp(b_imp);
disp('Impulse Invariance Denominator:');
disp(a_imp);

disp('Bilinear Transformation Numerator:');
disp(b_blt);
disp('Bilinear Transformation Denominator:');
disp(a_blt);

% Frequency responses
[H_imp, f] = freqz(b_imp, a_imp, 1024, Fs);
[H_blt, ~] = freqz(b_blt, a_blt, 1024, Fs);

% Plot graphs
figure;

subplot(2,1,1);
plot(f, abs(H_imp), 'b', 'LineWidth', 1.5);
hold on;
plot(f, abs(H_blt), 'r--', 'LineWidth', 1.5);
grid on;
title('Magnitude Response Comparison');
xlabel('Frequency (Hz)');
ylabel('Magnitude');
legend('Impulse Invariance', 'Bilinear Transformation');

subplot(2,1,2);
plot(f, angle(H_imp)*180/pi, 'b', 'LineWidth', 1.5);
hold on;
plot(f, angle(H_blt)*180/pi, 'r--', 'LineWidth', 1.5);
grid on;
title('Phase Response Comparison');
xlabel('Frequency (Hz)');
ylabel('Phase (Degrees)');
legend('Impulse Invariance', 'Bilinear Transformation');
```
## OUTPUT
<img width="474" height="403" alt="Screenshot 2026-10-13 095402" src="https://github.com/user-attachments/assets/a1ce75e3-796e-44f3-89eb-59d007651743" />
<img width="564" height="364" alt="Screenshot 2026-10-13 095344" src="https://github.com/user-attachments/assets/0ad76300-94d6-4a7a-8178-0d96a10b2715" />
