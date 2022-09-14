# TuMors_Detector
It is a program that was designed using Matlab using artificial intelligence algorithms and image processing, which aims to make the machine determine the areas of tumors in the brain after processing and filtering magnetic resonance images.
function varargout = prog1(varargin)
% PROG1 MATLAB code for prog1.fig
%      PROG1, by itself, creates a new PROG1 or raises the existing
%      singleton*.
%
%      H = PROG1 returns the handle to a new PROG1 or the handle to
%      the existing singleton*.
%
%      PROG1('CALLBACK',hObject,eventData,handles,...) calls the local
%      function named CALLBACK in PROG1.M with the given input arguments.
%
%      PROG1('Property','Value',...) creates a new PROG1 or raises the
%      existing singleton*.  Starting from the left, property value pairs are
%      applied to the GUI before prog1_OpeningFcn gets called.  An
%      unrecognized property name or invalid value makes property application
%      stop.  All inputs are passed to prog1_OpeningFcn via varargin.
%
%      *See GUI Options on GUIDE's Tools menu.  Choose "GUI allows only one
%      instance to run (singleton)".
%
% See also: GUIDE, GUIDATA, GUIHANDLES

% Edit the above text to modify the response to help prog1

% Last Modified by GUIDE v2.5 14-Sep-2022 18:24:01

% Begin initialization code - DO NOT EDIT
gui_Singleton = 1;
gui_State = struct('gui_Name',       mfilename, ...
                   'gui_Singleton',  gui_Singleton, ...
                   'gui_OpeningFcn', @prog1_OpeningFcn, ...
                   'gui_OutputFcn',  @prog1_OutputFcn, ...
                   'gui_LayoutFcn',  [] , ...
                   'gui_Callback',   []);
if nargin && ischar(varargin{1})
    gui_State.gui_Callback = str2func(varargin{1});
end

if nargout
    [varargout{1:nargout}] = gui_mainfcn(gui_State, varargin{:});
else
    gui_mainfcn(gui_State, varargin{:});
end
% End initialization code - DO NOT EDIT


% --- Executes just before prog1 is made visible.
function prog1_OpeningFcn(hObject, eventdata, handles, varargin)
% This function has no output args, see OutputFcn.
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
% varargin   command line arguments to prog1 (see VARARGIN)

% Choose default command line output for prog1
handles.output = hObject;

% Update handles structure
guidata(hObject, handles);

% UIWAIT makes prog1 wait for user response (see UIRESUME)
% uiwait(handles.figure1);


% --- Outputs from this function are returned to the command line.
function varargout = prog1_OutputFcn(hObject, eventdata, handles) 
% varargout  cell array for returning output args (see VARARGOUT);
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Get default command line output from handles structure
varargout{1} = handles.output;


% --- Executes on button press in edit1.
function edit1_Callback(hObject, eventdata, handles)
% hObject    handle to edit1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)




% --- Executes during object creation, after setting all properties.
function edit1_CreateFcn(hObject, eventdata, handles)
% hObject    handle to edit1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    empty - handles not created until after all CreateFcns called


% --- Executes on button press in pushbutton1.
function pushbutton1_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global img1 img2

[path,nofile] = imgetfile();
if nofile
    msgbox(sprint('image not found:( '),'Error','Warning');
    return
    
end

img1 = imread(path);
img1=im2double(img1);
img2=img1;

axes(handles.axes1);
imshow(img1);

title('\fontsize{20}\color[rgb]{0,1,1} Brain MRI')
        
% --- Executes on button press in pushbutton2.
function pushbutton2_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton2 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global img1
axes(handles.axes2)
if size(img1,3)==3
    img1=rgb2gray(img1);
end
K=medfilt2(img1);
axes(handles.axes2);
imshow(K);title('\fontsize{20}\color[rgb]{0,1,1} Med Filter');


% --- Executes on button press in pushbutton3.
function pushbutton3_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton3 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global img1
axes(handles.axes3);

if size(img1,3)==3
    img1=rgb2gray(img1);
end
K=medfilt2(img1);
C=double(K);

for i=1:size(C,1)-2
    for j=1:size(C,2)-2
        Gx=((2*C(i+2,j+1)+C(i+2,j)+C(i+2,j+2))-(2*C(i,j+1)+C(i,j)+C(i,j+2)));
        Gy=((2*C(i+1,j+2)+C(i,j+2)+C(i+2,j+2))-(2*C(i+1,j)+C(i,j)+C(i+2,j)));
        B(i,j)=sqrt(Gx.^2+Gy.^2);
        
    end
end
axes(handles.axes3)
imshow(B);title('\fontsize{20}\color[rgb]{0,1,1} Edge Detection')

% --- Executes on button press in pushbutton4.
function pushbutton4_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton4 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global img1
axes(handles.axes4);

if size(img1,3)==3
    img1=rgb2gray(img1);
    img1=medfilt2(img1);
    
end

imData=reshape(img1,[],1);
imData=double(imData);
[IDX nn]=kmeans(imData,4);
imIDX=reshape(IDX,size(img1));

bw=(imIDX==1);
se=ones(5);
bw=imopen(bw,se);
bw=bwareaopen(bw,400);
axes(handles.axes4)
imshow(bw);title('\fontsize{20}\color[rgb]{0,1,1} Segmentation')


% --- Executes on button press in pushbutton5.
function pushbutton5_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton5 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global img1
axes(handles.axes5);
K = medfilt2(img1);
bw=im2bw(K,0.7);
label=bwlabel(bw);

stats=regionprops(label,'Solidity','Area');
density=[stats.Solidity];
area=[stats.Area];
high_dense_area=density>0.5;
max_area=max(area(high_dense_area));
tumor_label=find(area==max_area);
tumor=ismember(label,tumor_label);

se=strel('square',5);
tumor=imopen(tumor,se);

Bound=bwboundaries(tumor,'noholes');

imshow(K);
hold on

for i=1:length(Bound)
    plot(Bound{i}(:,2),Bound{i}(:,1),'y','linewidth',1.75)
end

title('\fontsize{20}\color[rgb]{0,1,1} Tumor detected')

hold off
axes(handles.axes5)
