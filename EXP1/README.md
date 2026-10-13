## code
```

clear;
clc;
close all;

% Design Parameters
Fs = 8000;       % Sampling frequency in Hz
Ap = 1;          % Passband ripple in dB
As = 40;         % Stopband attenuation in dB

% ==========================================
% 1. LOW-PASS FILTER DESIGN
% ==========================================
fp_lp = 1000;    % LPF passband frequency (Hz)
fs_lp = 1500;    % LPF stopband frequency (Hz)

wp_lp = fp_lp / (Fs/2);
ws_lp = fs_lp / (Fs/2);

[N_lp, Wn_lp] = buttord(wp_lp, ws_lp, Ap, As);
[b_lp, a_lp] = butter(N_lp, Wn_lp, 'low');

% ==========================================
% 2. HIGH-PASS FILTER DESIGN
% ==========================================
fp_hp = 2500;    % HPF passband frequency (Hz)
fs_hp = 1800;    % HPF stopband frequency (Hz)

wp_hp = fp_hp / (Fs/2);
ws_hp = fs_hp / (Fs/2);

[N_hp, Wn_hp] = buttord(wp_hp, ws_hp, Ap, As);
[b_hp, a_hp] = butter(N_hp, Wn_hp, 'high');

% ==========================================
% DISPLAY NUMERICAL OUTPUTS
% ==========================================
fprintf('=== EXPERIMENT 1: BUTTERWORTH IIR FILTERS ===\n\n');

fprintf('--- LOW-PASS FILTER ---\n');
fprintf('Calculated Filter Order (N_lp): %d\n', N_lp);
fprintf('3-dB Cutoff Frequency (Wn_lp): %.4f Hz\n', ...
    Wn_lp * (Fs/2));

fprintf('Numerator Coefficients (b_lp):\n');
disp(b_lp);

fprintf('Denominator Coefficients (a_lp):\n');
disp(a_lp);

fprintf('\n--- HIGH-PASS FILTER ---\n');
fprintf('Calculated Filter Order (N_hp): %d\n', N_hp);
fprintf('3-dB Cutoff Frequency (Wn_hp): %.4f Hz\n', ...
    Wn_hp * (Fs/2));

fprintf('Numerator Coefficients (b_hp):\n');
disp(b_hp);

fprintf('Denominator Coefficients (a_hp):\n');
disp(a_hp);

% ==========================================
% FREQUENCY RESPONSE CALCULATION
% ==========================================
[H_lp, f] = freqz(b_lp, a_lp, 1024, Fs);
[H_hp, ~] = freqz(b_hp, a_hp, 1024, Fs);

% Avoid log10(0) when calculating magnitude in dB
mag_lp = 20*log10(max(abs(H_lp), 1e-10));
mag_hp = 20*log10(max(abs(H_hp), 1e-10));

% ==========================================
% PLOTTING
% ==========================================
figure('Name', 'Butterworth IIR LPF and HPF');

% LPF Magnitude Response
subplot(2,2,1);
plot(f, mag_lp, 'b', 'LineWidth', 1.5);
grid on;
title(['Butterworth LPF Magnitude (Order N = ', ...
    num2str(N_lp), ')']);
xlabel('Frequency (Hz)');
ylabel('Magnitude (dB)');
ylim([-80 5]);
xlim([0 Fs/2]);

% LPF Phase Response
subplot(2,2,2);
plot(f, unwrap(angle(H_lp))*180/pi, 'r', 'LineWidth', 1.5);
grid on;
title('Butterworth LPF Phase Response');
xlabel('Frequency (Hz)');
ylabel('Phase (degrees)');
xlim([0 Fs/2]);

% HPF Magnitude Response
subplot(2,2,3);
plot(f, mag_hp, 'b', 'LineWidth', 1.5);
grid on;
title(['Butterworth HPF Magnitude (Order N = ', ...
    num2str(N_hp), ')']);
xlabel('Frequency (Hz)');
ylabel('Magnitude (dB)');
ylim([-80 5]);
xlim([0 Fs/2]);

% HPF Phase Response
subplot(2,2,4);
plot(f, unwrap(angle(H_hp))*180/pi, 'r', 'LineWidth', 1.5);
grid on;
title('Butterworth HPF Phase Response');
xlabel('Frequency (Hz)');
ylabel('Phase (degrees)');
xlim([0 Fs/2]);
```

## Output
<img width="1047" height="193" alt="Screenshot 2026-10-13 093712" src="https://github.com/user-attachments/assets/89956562-3fad-4b45-8dae-cc8e898d27db" />
<img width="1026" height="427" alt="Screenshot 2026-10-13 093655" src="https://github.com/user-attachments/assets/ced01ba2-c91a-495a-a667-b0a0e0d076ea" />
<img width="665" height="588" alt="image" src="https://github.com/user-attachments/assets/a04c8f13-fc29-462a-9129-24f63ef39bfc" />



