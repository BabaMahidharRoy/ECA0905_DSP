## CODE
```
clc; clear; close all; 
%% IIR Filter Coefficients 
b = [0.0048 0.0193 0.0289 0.0193 0.0048]; 
a = [1 -2.3695 2.3140 -1.0547 0.1874]; 
N = 50; 
x = [1 zeros(1,N-1)];       
n = 0:N-1; 
% Unit impulse 
%% 1. Direct Form-I and Direct Form-II 
y1 = filter(b,a,x);         % Direct Form-I 
y2 = filter(b,a,x);         % Direct Form-II 
%% 2. Cascade Realization 
[sos,g] = tf2sos(b,a); 
yc = g*x; 
for k = 1:size(sos,1) 
    yc = filter(sos(k,1:3),sos(k,4:6),yc); 
end 
%% 3. Parallel Realization 
[r,p,k] = residuez(b,a); 
yp = filter(k,1,x); 
for i = 1:length(r) 
    yp = yp + filter(r(i),[1 -p(i)],x); 
end 
%% 4. Display Coefficients 
disp('Numerator coefficients b ='); disp(b) 
disp('Denominator coefficients a ='); disp(a) 
disp('Second Order Sections (Cascade) ='); disp(sos) 
disp('Cascade Gain ='); disp(g) 
disp('Residues (Parallel) ='); disp(r) 
disp('Poles (Parallel) ='); disp(p) 
disp('Direct term ='); disp(k) 
%% 5. Error and Percentage Deviation 
E = [max(abs(y1-y2)) 
    max(abs(y1-yc)) 
    max(abs(y1-yp))]; 
D = E/max(abs(y1))*100; 
T = table(["DF-I";"DF-II";"Cascade";"Parallel"], ... 
    [0;E], ... 
    [0;D], ... 
    'VariableNames',{'Structure','Maximum_Error','Deviation_percent'}); 
disp('Comparison Table:') 
disp(T) 
%% 6. Impulse Response Comparison 
figure; 
stem(n,y1,'filled'); hold on; 
stem(n,y2,'--'); 
stem(n,yc,':','LineWidth',1.5); 
stem(n,yp,'-.'); 
grid on; 
xlabel('n'); 
ylabel('Amplitude'); 
title('Impulse Response of IIR Realizations'); 
legend('Direct Form-I','Direct Form-II','Cascade','Parallel'); 
%% 7. Error Plot 
figure; 
plot(n,abs(y1-y2),'--','LineWidth',1.2); hold on; 
plot(n,abs(y1-yc),':','LineWidth',1.5); 
plot(n,abs(y1-yp),'-.','LineWidth',1.2); 
grid on; 
xlabel('n'); 
ylabel('Absolute Error'); 
title('Numerical Error'); 
legend('DF-I vs DF-II','DF-I vs Cascade','DF-I vs Parallel'); 
%% 8. Frequency Response 

figure; 
freqz(b,a,1024); 
title('Frequency Response of IIR Filter'); 

%% 9. Pole-Zero Plot 
figure; 
zplane(b,a); 
title('Pole-Zero Plot');
```
## OUTPUT
<img width="564" height="518" alt="Screenshot 2026-10-13 100422" src="https://github.com/user-attachments/assets/762ff27f-303e-400a-b160-7c997f943d64" />
<img width="496" height="253" alt="Screenshot 2026-10-13 100429" src="https://github.com/user-attachments/assets/eb494db8-c53c-456d-a8bc-3cdbeecb73f9" />
<img width="657" height="435" alt="Screenshot 2026-10-13 100444" src="https://github.com/user-attachments/assets/356389f3-b2e9-48e3-8f03-b95f1c7304f1" />
<img width="675" height="441" alt="Screenshot 2026-10-13 100454" src="https://github.com/user-attachments/assets/3781702c-245f-408f-b204-13b94a420c9d" />
<img width="681" height="448" alt="Screenshot 2026-10-13 100503" src="https://github.com/user-attachments/assets/9ab46350-da0d-4836-bb14-1612b565865f" />
<img width="629" height="445" alt="Screenshot 2026-10-13 100513" src="https://github.com/user-attachments/assets/feb649d1-d5aa-4ed1-9002-425fedc9cb81" />
