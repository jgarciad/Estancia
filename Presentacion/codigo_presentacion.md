\documentclass[10pt]{beamer}
\usepackage[utf8]{inputenc}
\usepackage{xeCJK}
\usepackage{graphicx}
\usepackage {mathtools}
\usepackage{utopia} %font utopia imported
\usepackage{subcaption}
\usepackage[export]{adjustbox}
\usepackage{wrapfig}
%\usepackage{subfigure}
\usetheme{CambridgeUS}
\usecolortheme{dolphin}


% set colors
\definecolor{myNewColorA}{RGB}{0,102,51}
\definecolor{myNewColorB}{RGB}{46,139,87}
\definecolor{myNewColorC}{RGB}{210,180,140}
\setbeamercolor*{palette primary}{bg=myNewColorC}
\setbeamercolor*{palette secondary}{bg=myNewColorB, fg=white}
\setbeamercolor*{palette tertiary}{bg=myNewColorA, fg=white}
\setbeamercolor*{titlelike}{fg=myNewColorA}
\setbeamercolor*{title}{bg=myNewColorA, fg=white}
\setbeamercolor*{item}{fg=myNewColorA}
\setbeamercolor*{caption name}{fg=myNewColorA}
\usefonttheme{professionalfonts}
\usepackage{natbib}
\usepackage{hyperref}
%------------------------------------------------------------
\titlegraphic{\includegraphics[height=1.5cm]{figures/images.png}}

\setbeamerfont{title}{size=\large}
\setbeamerfont{subtitle}{size=\small}
\setbeamerfont{author}{size=\small}
\setbeamerfont{date}{size=\small}
\setbeamerfont{institute}{size=\small}
\title[]{Desigualdad de género, marginalidad y su relación con el grosor cortical}
\subtitle{}
%\author[abc2-c]{Jorge Garcia Durante}
\author[]{Jorge García Durante \and Ezequiel Soto Sánchez}

\institute[]{Instituto Tecnológico Autónomo de México}
\date[Julio 3, 2024]
%{xxx Presentation 2023.3}

%------------------------------------------------------------
%This block of commands puts the table of contents at the 
%beginning of each section and highlights the current section:
%\AtBeginSection[]
%{
%  \begin{frame}
%    \frametitle{Contents}
%    \tableofcontents[currentsection]
%  \end{frame}
%}
\AtBeginSection[]{
  \begin{frame}
  \vfill
  \centering
  \begin{beamercolorbox}[sep=8pt,center,shadow=true,rounded=true]{title}
    \usebeamerfont{title}\insertsectionhead\par%
  \end{beamercolorbox}
  \vfill
  \end{frame}
}
%------------------------------------------------------------

\begin{document}

%The next statement creates the title page.
\frame{\titlepage}
\begin{frame}
\frametitle{Contenido}
\tableofcontents
\end{frame}
%------------------------------------------------------------
\section{Introducción}
    \begin{frame}{Introducción}
    \textbf{Objetivo:} explicar si niveles de desigualdad de género o marginación afectan la corteza cerebral en alguna zona en función del grosor cortical. El proceso para poder explicar si existe o no una relación es el siguiente:\newline
    \begin{itemize}
    \item Definir la región de interés (ROI)
    \item Correr el modelo de regresión sobre la región de interés  
    \item Determinar la tasa de descubrimiento falso FDR para distintos niveles de significancia de nuestras variables explicativas 
    \item En caso de que la variable sea significativa, mapear las zonas del cerebro afectadas 
    \item Determinar el efecto de cada variable y encontrar si existe diferencia entre hombres y mujeres 
    \end{itemize}
    \end{frame}
\section{Modelo}
    \begin{frame}{Modelo}
    El modelo propuesto considerando $n$ individuos con $p$ variables y $m$ vértices es el siguiente:\newline
    $$Y = X\beta + \epsilon$$\newline
    donde:
    \begin{itemize}
        \item $Y$ es una matriz de $n\times m$ vértices
        \item $\beta$ es una matriz de $p\times m$
        \item $X$ es un vector de $n\times p$
        \item $\epsilon$ es un vector de $n\times m$
    \end{itemize}\newline
    El método utilizado para encontrar $\beta$ se expresa de la siguiente manera:
    $$\beta = (X^{T}X)^{-1}X^{T}Y$$
    \end{frame}

    \begin{frame}{Modelo}
    Dado que tomamos decisiones con múltiples p − value estadísticamente es posible que tomemos falsos positivos, un método muy común para controlar la tasa de falsos positivos que estamos dispuestos a aceptar es usando pruebas FDR o False Discovery Rate por sus siglas en inglés.

    Definimos a la tasa FDR como:

    $$FDR = Q_e = E(Q) = E\left(\frac{V}{V+S}\right) = E\left(\frac{V}{R}\right)$$ \newline
    donde:
    \begin{itemize}
        \item $Q$ variable aleatoria
        \item $V$ variable aleatoria, número de $H_0$ erroneamente rechazadas
        \item $S$ variable aleatoria, número de $H_0$ correctamente rechazadas
        \item $R = V + S$ total de hipótesis rechazadas
    \end{itemize}\newline
    \end{frame}

\section{Variables}
    \begin{frame}{Variables}
    El Índice de Desigualdad de Género (IDG) refleja las desigualdades que experimentan las mujeres respecto a los hombres en tres dimensiones o subíndices: empoderamiento, participación en el mercado laboral y salud reproductiva. 

    El cáluclo del IDG, según la metodología global se determina de la siguiente manera:\newline

    $$IDG = 1-\frac{ARMO(G_M , G_H)}{G_{\bar{M},\bar{H}}}$$

    Donde $G_M$ y $G_H$ son las formúlas de agregación para las mujeres y hombres respectivamente. Dichas formulas de agregación contemplan tasas de moratlidad, fecundidad, representación en cabildos entre otras. Mientras mayor es el IDG mayor será el grado de desigualdad de un municipio. 
    
    \end{frame}

    \begin{frame}{Variables}
    Mapa de calor del IDG

    \begin{figure}[h]
        \begin{minipage}[c]{0.9\linewidth}
        \centering
        \includegraphics[height=6cm]{figures/MapacalorIDG.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    
    \end{frame}

    \begin{frame}{Variables}
    El índice de marginación es una medida-resumen que permite diferenciar las localidades del país según el impacto global de las carencias que padece la población como resultado de la falta de acceso a la educación, la residencia en viviendas inadecuadas y la carencia de bienes.

    Mediante el uso de Componentes Principales (CP) el IM se expresa como: 

    $$ IM_i^{CP} = \sum_{j=1}^9 \omega_j \frac{x_{ij}-\bar{x_j}}{S_j}$$

    donde $\omega_j$ representa las ponderaciones de la primera CP, $x_{ij}$ es el valor de la variable j-ésima en el territorio i-ésimo, $\bar{x_j}$ es el valor medio de la variable j-ésima, y $S_j$ es “la desviación estándar insesgada del indicador socioeconómico $j$”. Mientras menor sea el valor que toma el IM mayor será el nivel de marginación. 


    \end{frame}

    \begin{frame}{Variables}
    Mapa de calor del IM

    \begin{figure}[h]
        \begin{minipage}[c]{0.9\linewidth}
        \centering
        \includegraphics[height=6cm]{figures/MapacalorIM.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    
    \end{frame}
    
\section{Resultados}
    \begin{frame}{Resultados}
    En el caso del hemisferio izquierdo, el resultado de la prueba FDR es el siguiente:
    
    \begin{table}
        \centering
        \begin{tabular}{|c|c|c|c|c|}
        \hline 
        Treshold & F-statistic & tvalue- (Intercept) & tvalue-IDG & tvalue-IM\\ [0.5ex]
        \hline
            1\% & 18.371772 & 2.675596 & NA & 5.646297\\
            5\% & 18.371772 & 2.019872 & NA & 4.957493\\
            10\% & 4.252772 & 1.686800 & 2.387421 & 4.583074\\
            15\% & 2.938386 & 1.472713 & 1.962351 & 4.583074\\
            20\% & 2.368125 & 1.310805 & 1.684022 & 2.343637\\
        \hline
        \end{tabular}
        %\caption{}
        %\label{tab:my_label}
    \end{table}
    \end{frame}
    \begin{frame}{Resultados}
    En el caso del hemisferio derecho, el resultado de la prueba FDR es el siguiente:
    
    \begin{table}
        \centering
        \begin{tabular}{|c|c|c|c|c|}
        \hline 
        Treshold & F-statistic & tvalue- (Intercept) & tvalue-IDG & tvalue-IM\\ [0.5ex]
        \hline
            1\% & 11.363943 & 2.684823 & NA & 4.428994\\
            5\% & 8.553654 & 2.020289 & NA & 3.759492\\
            10\% & 3.821878 & 1.688829 & 2.271406 & 3.326305\\
            15\% & 2.896442 & 1.475385 & 1.849553 & 2.929601\\
            20\% & 2.314915 & 1.313015 & 1.617411 & 2.552914\\
        \hline
        \end{tabular}
        %\caption{}
        %\label{tab:my_label}
    \end{table}
    \end{frame}
    
    \begin{frame}{Resultados}
    Resultados al 10\%
    \begin{figure}[!h]
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/10idg.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/10im.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IM}
        \end{minipage}
        %\caption{}
    \end{figure}
    \end{frame}

    \begin{frame}{Resultados}
    Resultados al 15\%
    \begin{figure}[!h]
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/15idg.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/15im.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IM}
        \end{minipage}
        %\caption{}
    \end{figure}

    \end{frame}

    \begin{frame}{Resultados}
    Resultados al 20\%
    \begin{figure}[!h]
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/20idg.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.5\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/20im.png}
        \renewcommand{\figurename}{}
        \caption{Proyección IM}
        \end{minipage}
        %\caption{}
    \end{figure}

    \end{frame}

\section{Conclusiones}
    \begin{frame}{Conclusiones}
    Los resultados del modelo muestran que mientras mayores niveles de IDG estadísticamente se tiende a tener menores niveles de grosor cortical. De la misma forma, mientras menor es el valor del IM se observa un menor grosor cortical.

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/ResultadosIDG_grafica.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/ResultadosIM_grafica.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    En conclusión, mientras peor es la condición social ya sea por marginación o desigualdad de género, se observa un menor grosor cortical. 

\end{frame}

\section{Recomendaciones}
    \begin{frame}{Recomendaciones}
    Estadísticamente hablando los resultados son muy interesantes desde el contexto de lo que significa cada variable dentro del análisis. Sin embargo, como se muestra en la siguiente figura, no existe un argumento que permita concluir una diferencia clara entre hombres y mujeres.

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/idg_genero.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/im_genero.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}

    En ese orden de ideas aumentar la muestra de individuos dentro del estudio sin duda permitirá sacar conclusiones más contundentes respecto al género. 
    
\end{frame}

\section{Conclusiones}
    \begin{frame}{Conclusiones}
    Los resultados del modelo muestran que mientras mayores niveles de IDG estadísticamente se tiende a tener menores niveles de grosor cortical. De la misma forma, mientras menor es el valor del IM se observa un menor grosor cortical.

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/ResultadosIDG_grafica.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/ResultadosIM_grafica.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    En conclusión, mientras peor es la condición social ya sea por marginación o desigualdad de género, se observa un menor grosor cortical. 

\end{frame}

\section{Anexos}
    \begin{frame}{Anexos}
IDG Total (hombres y mujeres)

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Total.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Total_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}   
    \end{frame}

    \begin{frame}{Anexos}
IDG Hombres

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Hombres.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Hombres_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    \end{frame}

    \begin{frame}{Anexos}
IDG Mujeres

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Mujeres.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IDG_Mujeres_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    \end{frame}

    \begin{frame}{Anexos}
IM Total (hombres y mujeres)

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Total.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Total_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    \end{frame}

    \begin{frame}{Anexos}
IM Hombres

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Hombres.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Hombre_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    \end{frame}

   \begin{frame}{Anexos}
IM Mujeres

    \begin{figure}[!h]
        \begin{minipage}[c]{0.4\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Mujeres.png}
        %\caption{Proyección IDG}
        \end{minipage}\hfill
        \begin{minipage}[c]{0.49\linewidth}
        \centering
        \includegraphics[height=4cm]{figures/IM_Mujeres_R.png}
        %\caption{Proyección IM}
        \end{minipage}
        %\caption{Resultados tomando un treshold del 20\% en la prueba FDR}
    \end{figure}
    \end{frame}

\section*{Agradecimientos}  
    \begin{frame}
    \textcolor{myNewColorA}{\Huge{\centerline{Gracias!}}}
    \end{frame}

\end{document}



