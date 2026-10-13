## CODE
```

clc;
clear;
close all;

%% 1. INPUT PARAMETERS
Fs  = 1000;     % Sampling frequency (Hz)
Fp  = 100;      % Passband edge frequency (Hz)
Fst = 150;      % Stopband edge frequency (Hz)
Rp  = 1;        % Passband ripple (dB)
Rs  = 40;       % Stopband attenuation (dB)

% Normalized frequencies
Wp  = Fp/(Fs/2);
Wst = Fst/(Fs/2);

%% 2. CHEBYSHEV TYPE-I FORMULA
epsilon = sqrt(10^(Rp/10)-1);

N_formula = acosh(sqrt((10^(Rs/10)-1)/(10^(Rp/10)-1))) ...
            / acosh(Fst/Fp);

N_theory = ceil(N_formula);

%% 3. BUTTERWORTH FILTER
[Nb,Wb] = buttord(Wp,Wst,Rp,Rs);
[bB,aB] = butter(Nb,Wb,'low');

%% 4. CHEBYSHEV TYPE-I FILTER
[Nc,Wc] = cheb1ord(Wp,Wst,Rp,Rs);
[bC,aC] = cheby1(Nc,Rp,Wc,'low');

%% 5. ELLIPTIC FILTER
[Ne,We] = ellipord(Wp,Wst,Rp,Rs);
[bE,aE] = ellip(Ne,Rp,Rs,We,'low');

%% 6. DISPLAY INPUT PARAMETERS
fprintf('\n====================================================\n');
fprintf('       IIR FILTER COMPARATIVE ANALYSIS\n');
fprintf('====================================================\n');

fprintf('\nINPUT PARAMETERS\n');
fprintf('Sampling Frequency       = %.2f Hz\n',Fs);
fprintf('Passband Edge Frequency  = %.2f Hz\n',Fp);
fprintf('Stopband Edge Frequency  = %.2f Hz\n',Fst);
fprintf('Passband Ripple          = %.2f dB\n',Rp);
fprintf('Stopband Attenuation     = %.2f dB\n',Rs);

%% 7. CHEBYSHEV FORMULA RESULTS
fprintf('\nCHEBYSHEV TYPE-I FORMULA\n');
fprintf('Ripple factor epsilon    = %.4f\n',epsilon);
fprintf('Calculated order         = %.4f\n',N_formula);
fprintf('Theoretical minimum N    = %d\n',N_theory);

%% 8. FILTER ORDER COMPARISON
fprintf('\n====================================================\n');
fprintf('              FILTER ORDER COMPARISON\n');
fprintf('====================================================\n');

fprintf('Butterworth Order        = %d\n',Nb);
fprintf('Chebyshev Type-I Order   = %d\n',Nc);
fprintf('Elliptic Order           = %d\n',Ne);

%% 9. DISPLAY FILTER COEFFICIENTS
fprintf('\n====================================================\n');
fprintf('           BUTTERWORTH COEFFICIENTS\n');
fprintf('====================================================\n');
fprintf('Numerator b =\n');
disp(bB);
fprintf('Denominator a =\n');
disp(aB);

fprintf('\n====================================================\n');
fprintf('           CHEBYSHEV TYPE-I COEFFICIENTS\n');
fprintf('====================================================\n');
fprintf('Numerator b =\n');
disp(bC);
fprintf('Denominator a =\n');
disp(aC);

fprintf('\n====================================================\n');
fprintf('             ELLIPTIC COEFFICIENTS\n');
fprintf('====================================================\n');
fprintf('Numerator b =\n');
disp(bE);
fprintf('Denominator a =\n');
disp(aE);

%% 10. FREQUENCY RESPONSES
N = 4096;

[HB,f] = freqz(bB,aB,N,Fs);
[HC,~] = freqz(bC,aC,N,Fs);
[HE,~] = freqz(bE,aE,N,Fs);

MB = 20*log10(abs(HB)+eps);
MC = 20*log10(abs(HC)+eps);
ME = 20*log10(abs(HE)+eps);

%% 11. PASSBAND RIPPLE
P = f <= Fp;

Ripple_B = max(MB(P))-min(MB(P));
Ripple_C = max(MC(P))-min(MC(P));
Ripple_E = max(ME(P))-min(ME(P));

%% 12. STOPBAND ATTENUATION
S = f >= Fst;

Stop_B = -max(MB(S));
Stop_C = -max(MC(S));
Stop_E = -max(ME(S));

%% 13. PERCENTAGE DEVIATION
Dev_C_Ripple = abs(Ripple_C-Rp)/Rp*100;

Dev_B_Stop = abs(Stop_B-Rs)/Rs*100;
Dev_C_Stop = abs(Stop_C-Rs)/Rs*100;
Dev_E_Stop = abs(Stop_E-Rs)/Rs*100;

%% 14. TRANSITION CHARACTERISTICS
TransitionBW = Fst-Fp;

%% 15. DISPLAY PERFORMANCE
fprintf('\n====================================================\n');
fprintf('             FILTER PERFORMANCE\n');
fprintf('====================================================\n');

fprintf('\nPASSBAND RIPPLE\n');
fprintf('Butterworth      = %.4f dB\n',Ripple_B);
fprintf('Chebyshev Type-I = %.4f dB\n',Ripple_C);
fprintf('Elliptic         = %.4f dB\n',Ripple_E);

fprintf('\nSTOPBAND ATTENUATION\n');
fprintf('Butterworth      = %.4f dB\n',Stop_B);
fprintf('Chebyshev Type-I = %.4f dB\n',Stop_C);
fprintf('Elliptic         = %.4f dB\n',Stop_E);

fprintf('\nCHEBYSHEV RIPPLE DEVIATION\n');
fprintf('Specified Ripple    = %.2f dB\n',Rp);
fprintf('Measured Ripple     = %.4f dB\n',Ripple_C);
fprintf('Percentage Deviation = %.4f %%\n',Dev_C_Ripple);

fprintf('\nSTOPBAND DEVIATION\n');
fprintf('Butterworth Deviation = %.4f %%\n',Dev_B_Stop);
fprintf('Chebyshev Deviation   = %.4f %%\n',Dev_C_Stop);
fprintf('Elliptic Deviation    = %.4f %%\n',Dev_E_Stop);

fprintf('\nTRANSITION CHARACTERISTICS\n');
fprintf('Passband Edge    = %.2f Hz\n',Fp);
fprintf('Stopband Edge    = %.2f Hz\n',Fst);
fprintf('Transition Band  = %.2f Hz\n',TransitionBW);

%% 16. MAGNITUDE RESPONSE
figure;

plot(f,MB,'LineWidth',1.5);
hold on;
plot(f,MC,'LineWidth',1.5);
plot(f,ME,'LineWidth',1.5);

grid on;
xlabel('Frequency (Hz)');
ylabel('Magnitude (dB)');
title('Comparison of IIR Filter Magnitude Responses');

legend('Butterworth','Chebyshev Type-I','Elliptic', ...
       'Location','southwest');

xlim([0 250]);
ylim([-80 5]);

xline(Fp,'--','Fp = 100 Hz');
xline(Fst,'--','Fst = 150 Hz');
yline(-Rp,'--','Rp = -1 dB');
yline(-Rs,'--','Rs = -40 dB');

hold off;

%% 17. PHASE RESPONSE
Phase_B = unwrap(angle(HB))*180/pi;
Phase_C = unwrap(angle(HC))*180/pi;
Phase_E = unwrap(angle(HE))*180/pi;

figure;

plot(f,Phase_B,'LineWidth',1.2);
hold on;
plot(f,Phase_C,'LineWidth',1.2);
plot(f,Phase_E,'LineWidth',1.2);

grid on;
xlabel('Frequency (Hz)');
ylabel('Phase (degrees)');
title('Comparison of IIR Filter Phase Responses');

legend('Butterworth','Chebyshev Type-I','Elliptic');
xlim([0 250]);

hold off;

%% 18. FILTER ORDER GRAPH
figure;

bar([Nb Nc Ne]);

set(gca,'XTick',1:3);
set(gca,'XTickLabel', ...
    {'Butterworth','Chebyshev-I','Elliptic'});

ylabel('Filter Order');
xlabel('Filter Type');
title('Comparison of IIR Filter Orders');
```
## OUTPUT
<img width="491" height="491" alt="Screenshot 2026-10-13 095811" src="https://github.com/user-attachments/assets/cdff8371-001a-4afc-9577-6dc236002342" />
<img width="937" height="473" alt="Screenshot 2026-10-13 095826" src="https://github.com/user-attachments/assets/d215d24c-918c-4b3e-a57b-b19f6802adfa" />
<img width="671" height="430" alt="Screenshot 2026-10-13 095837" src="https://github.com/user-attachments/assets/7665a396-686a-498c-ae92-361e87f01aea" />
<img width="456" height="596" alt="Screenshot 2026-10-13 095847" src="https://github.com/user-attachments/assets/7b7d2d65-4804-409f-a5b3-d3bb68d8e0e2" />
<img width="708" height="435" alt="Screenshot 2026-10-13 095910" src="https://github.com/user-attachments/assets/fdcafd09-5592-41f4-9b7c-082cf2007bec" />
<img width="675" height="445" alt="Screenshot 2026-10-13 095922" src="https://github.com/user-attachments/assets/0042a127-9b14-4720-ada0-87edaeb962b1" />
<img width="706" height="446" alt="Screenshot 2026-10-13 095935" src="https://github.com/user-attachments/assets/dcf8d01c-57d9-4e12-9474-3eeb5d3fe9fe" />


grid on;
