%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% LaTeX Template: Project Titlepage
%
% Source: http://www.howtotex.com
% Date: April 2011
% 
% This is a title page template which be used for articles & reports.
% 
% Feel free to distribute this example, but please keep the referral
% to howtotex.com
% 
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% How to use writeLaTeX: 
%
% You edit the source code here on the left, and the preview on the
% right shows you the result within a few seconds.
%
% Bookmark this page and share the URL with your co-authors. They can
% edit at the same time!
%
% You can upload figures, bibliographies, custom classes and
% styles using the files menu.
%
% If you're new to LaTeX, the wikibook is a great place to start:
% http://en.wikibooks.org/wiki/LaTeX
%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%
% --------------------------------------------------------------------
% Preamble
% --------------------------------------------------------------------
\documentclass[paper=a4, fontsize=11pt,twoside]{scrartcl}	% KOMA

\usepackage[a4paper,pdftex]{geometry}	% A4paper margins
\usepackage{mathtools}
\setlength{\oddsidemargin}{5mm}			% Remove 'twosided' indentation
\setlength{\evensidemargin}{5mm}

\usepackage[english]{babel}


\usepackage{romannum}
\usepackage{bm}

\usepackage[protrusion=true,expansion=true]{microtype}	
\usepackage{amsmath,amsfonts,amsthm,amssymb}
\usepackage{graphicx}
\usepackage{subcaption}
% --------------------------------------------------------------------
% Definitions (do not change this)
% --------------------------------------------------------------------
\newcommand{\HRule}[1]{\rule{\linewidth}{#1}} 	% Horizontal rule

\makeatletter							% Title
\def\printtitle{%						
    {\centering \@title\par}}
\makeatother									

\makeatletter							% Author
\def\printauthor{%					
    {\centering \large \@author}}				
\makeatother							

% --------------------------------------------------------------------
% Metadata (Change this)
% --------------------------------------------------------------------
\title{	%\normalsize \textsc{Analysis} 	% Subtitle
		 	%\\[2.0cm]								% 2cm spacing
			\vspace*{1.7cm}
			\HRule{0.5pt} \\						% Upper rule
			\LARGE \textbf{\uppercase{ANALYSIS \Romannum{1}}}\\[0.3cm]	% Title
			\normalsize\textbf{Proofs of the exercises}
			\HRule{2.0pt} \\ [0.5cm]		% Lower rule + 0.5cm spacing
			\normalsize \today			% Todays date
		}

\author{
		EGU1832\\	
		Sogang university\\	
		Department of computer science\\
        \texttt{skygrid1832@gmail.com} \\
}

\begin{document}
% ------------------------------------------------------------------------------
% Maketitle
% ------------------------------------------------------------------------------
\thispagestyle{empty}		% Remove page numbering on this page

\printtitle					% Print the title data as defined above
  	\vfill
\printauthor				% Print the author data as defined above
\newpage
% ------------------------------------------------------------------------------
\setcounter{page}{1}		% Set page numbering to begin on this page
\section{The real number system}
\subsection{Exercise 1.8}
Show that ${\sup\bm{E}}$ is unique.
\begin{proof}
Suppose $\alpha_1, \alpha_2 = supE$ where $E\subset S$ and S is ordered set. \\
\indent From the definition of supremum, $\alpha_1$ and $\alpha_2$ are both upper bound of $E$. \\
\indent By that definition: \\
\indent\indent $\alpha_1$ is supremum of $E$ and $\alpha_2$ is upper bound of $E$ implies $\alpha_1 \leq \alpha_2$. \\
\indent\indent $\alpha_2$ is supremum of $E$ and $\alpha_1$ is upper bound of $E$ implies $\alpha_2 \leq \alpha_1$. \\
\indent So: \\
\indent\indent $\alpha_1 \leq \alpha_2$, $\alpha_2 \leq \alpha_1$\\
\indent thus $\alpha_1 = \alpha_2$
\end{proof}

\subsection{Exercise 1.13}
Define the \textbf{greatest-lower-bound-property}. \\
We can define \textbf{greatest-lower-bound-property} if following is true: \\
\indent If $E \subset S$ is not empty and $E$ is bounded below, then $\inf E$ exists in $S$.

\subsection{Exercise 1.24}
Is it natural to define ${\inf\emptyset=\infty}$?
\textbf{yes}. It is natural to define $\inf\emptyset = \infty$ since all elements in $\overline{\mathbb{R}}$ can be lower bound of the empty set.

\subsection{Exercise 1.25}
Define
\[\inf\emptyset=\infty\text{ and }\sup\emptyset=-\infty\]
Show that every subset of $\overline{\mathbb{R}}$ has a greatest lower bound. \\
\begin{proof}
Let $E$ be subset of $\overline{\mathbb{R}}$. \\
\indent $(i)$ If $E$ is an empty set $\emptyset$: \\
\indent\indent\indent There exists \textbf{greatest lower bound} since we defined $\inf\emptyset = \infty$ \\
\indent $(ii)$ If $E$ is not an empty set: \\
\indent\indent\indent Since $\overline{\mathbb{R}}$ is an ordered set, subset $E$ always has lower bound. \\
\indent\indent\indent So, from the definition of \textbf{greatest-lower-bound-property}, \\
\indent\indent\indent there exists \textbf{greatest lower bound} \\
\indent thus every subset of $\overline{\mathbb{R}}$ has a greatest lower bound.
\end{proof}

\subsection{Exercise 1.28}
Can we give an order on ${\mathbb{R}^2}$? \\
\indent $\mathbb{R}^2$ is a Cartesian product of $\mathbb{R}$. In other words,
$$\mathbb{R}^2 := \{ (x, y) : x \in \mathbb{R}, y \in \mathbb{R} \}$$
\indent For ordered pair $(x_1, y_1), (x_2, y_2)$, \\
\indent we can't define order in the condition: \\
\indent\indent $x_1 > x_2 \land y_1 < y_2$ or $x_1 < x_2 \land y_1 > y_2$ \\
\indent but we can order in the condition: \\
\indent\indent $x_1 = x_2$ or $y_1 = y_2$ \\
\indent So: \\
\indent\indent ${\mathbb{R}^2}$ is partially ordered set.

\section{Basic topology}
\subsection{Exercise 2.2}
Prove the following properties of the relation.
\begin{enumerate}
\item{It is reflective : ${A\sim A}$}
\item{It is symmetric : If ${A\sim B}$, then ${B\sim A}$}
\item{It is transitive : If ${A\sim B}$ and ${B\sim C}$, then ${A\sim C}$}
\end{enumerate}


\subsection{Exercise 2.4}
Let ${A}$ and ${B}$ be two finite sets. Assume that ${A}$ and ${B}$ contain the same number of elements. Show that ${A\sim B}$.
\begin{proof}We can show 1-1 mapping from $A$ to $B$ since $A$ and $B$ have same number of elements and both are finite sets. \\
\indent $\therefore$ $A$ and $B$ have same cardinal number, which is $A\sim B$
\end{proof}

\subsection{Exercise 2.5}
Let ${A}$ be an infinite set. Show that the number of elements of ${A}$ is infinite.
\begin{proof} The cardinality of infinite set $A$ is infinite, $n(A)=\infty$. Because number of elements of $A$ is unlimited in it. \\
\indent $\therefore$ the number of elements of $A$ is infinite.
\end{proof}

\subsection{Exercise 2.6}
Let ${A}$ be a finite set and ${B}$ be a proper subset of ${A}$, that is, ${B\subset A}$ and ${B\neq A}$. Show that ${A}$ and ${B}$ cannot be equivalent.
\begin{proof} Since $B$ is subset of $A$, we can match same elements of $A$ and $B$ 1-1. \\
\indent But we can't match every element 1-1 because $A \neq B$. \\
\indent $\therefore$ $A$ and $B$ cannot be equivalent.
\end{proof}

\subsection{Exercise 2.8}
Let ${A}$ be a countable set. Show that ${A}$ is infinite.
\begin{proof} $A$ is countable means $A\sim \mathbb{N}$. \\
\indent A is finite is $A\sim J_n$ for some $n \in \mathbb{N}$. \\
\indent But A is $A\sim J_n$ for every $n \in \mathbb{N}$ \\
\indent $\therefore$ $A$ is infinite.
\end{proof}

\subsection{Exercise 2.18}
Let ${A}$ be a at most countable set. Assume that for each ${\alpha\in A}$,there exists a at most countable set ${B_{\alpha}}$. Show
\[\bigcup\limits_{\alpha\in A}B_{\alpha}\]
is at most countable.
\begin{proof}
At most countable means set $A$ is finite or countable. \\
\indent $(i)$ If $A$ is finite set, \\
\indent\indent $\bigcup\limits_{\alpha\in A}B_{\alpha}$ is countable since $B_{\alpha}$ is countable and $A$ is finite. \\
\indent $(ii)$ If $A$ is countable set, \\
\indent\indent We can number $\alpha$ 1 to $n$, and we can also number elements of $B_{\alpha}$ \\
\indent\indent as $b_{\alpha 1}, b_{\alpha 2}, ...$ in the same way. \\
\indent\indent Using \emph{diagonal argument}, we can count $\bigcup\limits_{\alpha\in A}B_{\alpha}$. \\
\indent So: \\
\indent\indent $\bigcup\limits_{\alpha\in A}B_{\alpha}$ is countable.
\end{proof}

\subsection{Exercise 2.23}
Assume that every real number has the binary representation. Show that the real number system ${\mathbb{R}}$ is uncountable.
\begin{proof}
Let $\mathbb{R}$ be set $A$. Elements of set $A$ can be aligned like this: \\
\indent $a_1\quad 0101...$ \\
\indent $a_2\quad 1001...$ \\
\indent $a_3\quad 1100...$ \\
\indent $:$ \\
\indent Make a number $b$ that $n$th number of $b$ is $n$th opposite number of $a_n$. \\
\indent Then, we can't decide which $a_n$ to place $b$. \\
\indent That is, we can't match $\mathbb{R}$ to $J_n$ properly. \\
\indent $\therefore$ $\mathbb{R}$ is uncountable.
\end{proof}

\subsection{Exercise 2.28}
Show that k-cells are convex.
\begin{proof}
Let $C=\prod_{n=1}^{k}I_n$. $I_n$ is interval of $\mathbb{R}^n$. \\
\indent Let $x, y \in C$ where $x = (x_1, x_2, ... , x_k)$, and $y = (y_1, y_2, ... , y_k)$. \\
\indent Let $z_n = \lambda x_n + (1 - \lambda)y_n$, \\
\indent then $z = (\lambda x_1 + (1 - \lambda)y_1, \lambda x_2 + (1 - \lambda)y_2, ... , \lambda x_k + (1 - \lambda)y_k)$. \\
\indent We can see each components of $z$ is respectively between $x_n$ and $y_n$. \\
\indent So components of $z$ belongs to $I_n$ respectively. \\
\indent $\therefore$ k-cells are convex.
\end{proof}

\subsection{Exercise 2.34}
Let
\[E:=\frac{1}{n}:n\in\mathbb{N}\]
Prove the following.
\begin{enumerate}
\item{${E}$ has only one limit point 0}
\item{${E}$ has no interior point of ${E}$}
\item{${E}$ is not closed}
\item{${E}$ is not open}
\item{${E}$ is not perfect}
\end{enumerate}

\subsection{Exercise 2.41}
Let ${n\in\mathbb{N}}$ and assume that there exist closed sets ${F_1,\cdots,F_n}$. Show
\[\bigcup\limits_{i=1}^nF_i\]
is closed.

\subsection{Exercise 2.43}
Prove or disprove that for a sequence of closed sets ${F_1,F_2,\cdots,}$
\[\bigcup\limits_{n=1}^{\infty}F_n\]
is closed or open.

\subsection{Exercise 2.47}
Let (${X}$,${d}$) be metric space, ${E\subset X}$, and x be a point in ${X}$. Assume that ${x\notin E}$ and ${x}$ is not a limit point of ${E}$. Show the existence of ${r>0}$ such that ${N_r(x)\cap\bar{E}=\emptyset}$

\subsection{Exercise 2.51}
Prove or disprove that
\begin{enumerate}
\item{${N_r^Y(p)}$ is a neighborhood in ${X}$}
\item{${N_r^Y(p)}$ is an open set in ${X}$}
\end{enumerate}

\subsection{Exercise 2.57}
Define a finite subcover of ${E}$ relative to ${Y}$.

\subsection{Exercise 2.60}
Let ${X}$ be a metric space, ${K\subset X}$, ${p\in K^c}$, and ${q\in K}$. Show that for all ${0<r\leq\frac{1}{2}d(p,q)}$, two neighborhoods ${N_r(p)}$ and ${N_r(q)}$ are disjoint.

\subsection{Exercise 2.64}
Let ${\{K_\alpha:\alpha \in A\}}$ be a collection of closed supsets of a metric space ${X}$. Assume that the intersection of every finite subcollection of ${K_\alpha:\alpha\in A}$ is nonempty, i.e. for any finite ${B\subset A}$,
\[\bigcap\limits_{\alpha\in B}K_\alpha\neq\emptyset\]
prove or disprove that
\[\bigcap\limits_{\alpha\in A}K_\alpha\neq\emptyset\]

\subsection{Exercise 2.65}
Let ${\{K_\alpha:\alpha \in A\}}$ be a collection of open supsets of a metric space ${X}$. Assume that the intersection of every finite subcollection of ${K_\alpha:\alpha\in A}$ is nonempty, i.e. for any finite ${B\subset A}$,
\[\bigcap\limits_{\alpha\in B}K_\alpha\neq\emptyset\]
prove or disprove that
\[\bigcap\limits_{\alpha\in A}K_\alpha\neq\emptyset\]

\subsection{Exercise 2.67}
Let ${K_1,K_2,\dotsm}$ be a sequence of nonempty compact sets such that ${K_n\supset K_{n+1}}$ for all ${n\in\mathbb{N}}$. Show
\[\bigcap\limits_{n=1}^{\infty}K_n\neq\emptyset\]

\subsection{Exercise 2.70}
Let ${\{[a_n,b_n]:n\in\mathbb{N}\}}$ be a sequence of closed intervals. Assume that
\[a_n\leq b_m\]
for all ${n,m\in\mathbb{N}}$ and define ${E=\{b_n:n\in\mathbb{N}\}}$.
\begin{enumerate}
\item{Show that\[\inf E\in\bigcap\limits_{n=1}^{\infty}I_n\]}
\item{Prove or disprove that\[\inf E=\sup\{a_n:n\in\mathbb{N}\}\]}
\end{enumerate}

\subsection{Exercise 2.75}
Let ${(X,d)}$ be a metric space, ${E\subset X}$, and ${x\in X}$. Assume that there exists a positive constant ${r_0}$ such that for any ${r<r_0}$, there exists ${z\in E}$ such that ${z\neq x}$ and ${|x-z|<r}$. Show that ${x}$ is a limit point of ${E}$.

\subsection{Exercise 2.77}
Let ${k\in\mathbb{N}}$ and ${E\subset\mathbb{R}^k}$. Assume that ${E}$ is bounded. Show that there exists a k-cell ${I}$ such that ${E\subset I}$.

\subsection{Exercise 2.78}
Let ${k\in\mathbb{N}}$ and ${E\subset\mathbb{R}^k}$. Assume that ${E}$ is not bounded.
\begin{enumerate}
\item{Show that there exists a sequence ${x_1,x_2,\dotsm}$ such that\[x_i\neq x_j\text{   }\forall i\neq j\]${x_n\in E}$ and\[|x_n|>n\text{   }\forall n\in\mathbb{N}\]}
\item{Additionally, prove that the set\[S:=\{x_n:n\in\mathbb{N}\}\]is infinite.}
\end{enumerate}

\subsection{Exercise 2.81}
Let ${k\in\mathbb{N},E\subset K\subset\mathbb{R}^k}$. Assume that ${K}$ is compact. Prove or disprove the following three statements:
\begin{enumerate}
\item{Every subsets of 	${E}$ has a limit point in ${E}$}
\item{Every subsets of ${E}$ has a limit point in ${K}$}
\item{${E}$ is compact}
\end{enumerate}

\section{Numerical sequences and series}
\subsection{Exercise 3.2}
Let ${X_1=\mathbb{R}}$, ${X_2=(0,\infty)}$, and ${p_n=\frac{1}{n}}$ for all ${n\in\mathbb{N}}$. Show that the sequence ${\{p_n\}}$ converges in ${X_1}$ but diverges in ${X_2}$.

\subsection{Exercise 3.3}
Let a be a nonnegative constant. Assume that for all ${\epsilon>0}$.
\[\alpha<\epsilon\]
Show that ${\alpha=0}$.

\subsection{Exercise 3.7}
Let ${(X,d)}$ be a metric space, ${E\subset X}$, and ${p\in X}$. Assume that there exists a sequence ${\{p_n\}}$ in ${E}$ such that ${\lim\limits_{n\to\infty}p_n=p}$. Prove or disprove that ${p}$ is a limit point of ${E}$.

\subsection{Exercise 3.9}
Let ${(X,d)}$ be a metric space, ${\{p_n\}}$ be a sequence in ${X}$, and ${p\in X}$. Assume that
\[p=p_j\]
for infinitely many ${j}$, i.e. the set ${\{j\in\mathbb{N}:p=p_j\}}$ is infinite. Show that there exists a subsequence ${\{p_{n_i}:i\in\mathbb{N}\}}$ of ${\{p_n:n\in\mathbb{N}\}}$ such that
\[\lim\limits_{i\to\infty}p_{n_i}=p\]

\subsection{Exercise 3.10}
Let ${(X,d)}$ be a metric space, ${\{p_n\}}$ be a sequence in ${X}$, and ${p\in X}$. Assume that
\[p=p_j\]
for infinitely many ${j}$, i.e. the set ${j\in\mathbb{N}:p=p_j}$ is infinite. Show that there exists a subsequence ${\{p_{n_i}:i\in\mathbb{N}\}}$ of ${p_n:n\in\mathbb{N}}$ such that
\[\lim\limits_{i\to\infty}p_{n_i}=p\]

\subsection{Exercise 3.11}
Let ${(X,d)}$ be a metric space, ${\{p_n\}}$ be a sequence in ${X}$, ${p\in X}$, and ${\{\tilde{p_n}\}}$ is a sequence in ${\{p_n:n\in\mathbb{N}\}}$, i.e.
\[\{\tilde{p_n}:n\in\mathbb{N}\}\subset\{p_n:n\in\mathbb{N}\}\]
Assume that
\[\lim\limits_{n\to\infty}\tilde{p_n}=p\]
Show that there exists a subsequence ${\{p_{n_i}\}}$ of ${\{p_n\}}$ such that
\[\lim\limits_{i\to\infty}p_{n_i}=p\]

\subsection{Exercise 3.15}
Let ${(X,d)}$ be a metric space, ${K\subset X}$. Assume that ${K}$ is sequentially compact. Prove or disprove the following two statements (cf. Theorem 2.80)
\begin{enumerate}
\item{${K}$ is bounded}
\item{${K}$ is closed}
\end{enumerate}

\subsection{Exercise 3.16}
Let ${(X,d)}$ be a metric space, ${E\subset X}$, and ${p\in X}$. Assume that there exists a sequence ${p_n}$ in ${E}$ such that
\[d(p_n,p)<\frac{1}{n}\text{   }\forall n\in\mathbb{N}\]
Show that
\[\lim\limits_{n\to\infty}p_n=p\]

\subsection{Exercise 3.20}
Let ${(X,d)}$ be a metric space, ${\{p_n\}}$ be a sequence in ${X}$, and ${p\in X}$. Assume that ${\{p_n\}}$ converges to ${p}$. Then for all positive constants ${\epsilon_1}$ and ${\epsilon_2}$, there exists a positive integer ${N}$ such that for all ${n\geq N}$
\[d(p_n,p)<\epsilon_1\epsilon_2\]

\subsection{Exercise 3.25}
Let ${(X,d)}$ be a metric space, ${E\subset X}$, and ${E_1\subset E_2\subset X}$. Show that ${\bar{E_1}\subset\bar{E_2}}$.

\subsection*{Theorem 3.26}
Let ${(X,d)}$ be a metric space and ${\{p_n\}}$ be a sequence in ${X}$. Assume that ${X}$ is compact and ${\{p_n\}}$ is a Cauchy sequence. Then ${\{p_n\}}$ converges. In other words, every Cauchy sequence in a compact metric space converges.
\subsubsection*{Proof}
Define ${E_n=\{p_n,p_{n+1},p_{n+2},\dotsm\}}$. ${\bar{E_n}}$ is closed for all ${n\in\mathbb{N}}$. Since ${X}$ is compact and ${E_n\subset X}$, ${\bar{E_n}}$ is compact for all ${n\in\mathbb{N}}$. Moreover, obviously
\[\bar{E_n}\supset\bar{E_{n+1}}\text{   }\forall n\in\mathbb{N}\]
Thus
\[\bigcap\limits_{n=1}^\infty\bar{E_n}\neq\emptyset\]
and choose ${p\in\bigcap\limits_{n=1}^\infty\bar{E_n}}$. Let ${\epsilon>0}$ be given. Then since ${\{p_n\}}$ is a Cauchy sequence, there exists ${N\in\mathbb{N}}$ such that for all ${n,m\geq N}$.
\[d(p_n,p_m)<\frac{\epsilon}{2}\]
Since ${p\in\bar{E_N}}$, there exists ${p_{n_0}}$ such that ${n_0\geq N}$ and ${d(p,p_{n_0})<\frac{\epsilon}{2}}$. Therefore by the triangle inequality, for all ${n\geq N}$ we have
\[d(p,p_n)\leq d(p,p_{n_0})+d(p_{n_0},p_n)<\frac{\epsilon}{2}+\frac{\epsilon}{2}=\epsilon\]


\subsection{Exercise 3.27}
In the proof of theorem 3.24, show that there exists ${p\in X}$ such that
\[\bigcap\limits_{n=1}^{\infty}\bar{E_n}=\{p\}\]

\subsection*{Theorem 3.13}
Let ${(X,d)}$ be a metric space and ${K\subset X}$. Assume that ${K}$ is compact. Then ${K}$ is sequentially compact.
\subsection{Exercise 3.28}
Prove thoerem 3.24 by using theorem 3.13

\subsection*{Corollary 3.29}
Let ${k\in\mathbb{N}}$ and ${\{p_n\}}$ be a sequence in ${\mathbb{R}^k}$. Assume that ${\{p_n\}}$ is a Cauchy sequence. Then ${\{p_n\}}$ converges. In other words, every Cauchy sequence in ${\mathbb{R}^k}$ converges.
\subsection{Exercise 3.30}
\begin{enumerate}
\item{Prove Corollary 3.29 by using Theorem 3.26.}
\item{Prove Corollary 3.29 without using Theorem 3.26.}
\end{enumerate}

\subsection{Exercise 3.33}
Let ${\mathbb{Q}}$ be the rational number system with the standard metric, i.e. for all ${x,y\in\mathbb{Q}}$, the metric ${d}$ in ${\mathbb{Q}}$ is given by
\[d(x,y)=|x-y|-\Bigg(\sum\limits_{i=1}^k(x_i-y_i)^2\Bigg)^{1/2}\]
Show that ${\mathbb{Q}}$ is not complete.

\subsection{Exercise 3.37}
Prove the following. If ${\inf A\in\mathbb{R}}$, then for any ${\epsilon>0}$, there exists an ${b\in A}$ such that
\[b<\inf A+\epsilon\]

\subsection{Exercise 3.40}
Let ${\{a_n\}}$ be a sequence in ${\mathbb{R}}$. Assume that
\[\lim\limits_{n\to\infty}\sup a_n=\lim\limits_{n\to\infty}\inf a_n\]
Show that ${\{a_n\}}$ converges.

\subsection{Exercise 3.41}
Prove Lemma 3.38(b).

\subsection{Exercise 3.43}
Let ${\{a_n\}}$ be a sequence in ${\mathbb{R}^k}$. Show that the series ${\sum\limits_{n=1}^\infty a_n}$ converges to ${s}$ if and only if for any ${k\in\mathbb{N}}$ the series ${\sum\limits_{n=k}^\infty a_n}$ converges to
\[s-\sum\limits_{n=1}^k a_n\]

\subsection{Exercise 3.45}
Let ${\{a_n\}}$ be a sequence in ${\mathbb{R}^k}$ and ${\{s_n\}}$ be its partial sums. Show that ${\{s_n\}}$ is a Cauchy sequence in ${\mathbb{R}^k}$ if and only if for every ${\epsilon>0}$ there exists a positive integer ${N}$ such that for all ${m\geq n \geq N}$
\[\bigg|\sum\limits_{k=n}^ma_k\bigg|<\epsilon\]

\newpage

\section{Continuity}

\subsection{Exercise 4.3}
Missing ${\epsilon}$ in the problem. Type error in the problem.
\subsection{Exercise 4.4}
Prove or disprove that
\[\lim\limits_{n\to\infty}f(p_n)=q\]
for every sequence ${\{p_n\}}$ in ${E}$ such that
\[p_n\neq p,\text{   }\lim\limits_{n\to\infty}p_n=p\]
if and only if
\[\lim\limits_{n\to\infty}f(p_n)=q\]
for every sequence ${\{p_n\}}$ in ${E}$ such that
\[\lim\limits_{n\to\infty}p_n=p\]

\subsection{Exercise 4.5}
Let ${X}$ be a metric space, ${E\subset X}$, and ${p}$ be a limit point of ${E}$. Show that there exists a sequence ${\{p_n\}}$ in ${E}$ such that
\[p_n\neq p\text{   }\forall n \in\mathbb{N}\]
and
\[\lim\limits_{n\to\infty}p_n=p\]
(cf.Theorem 2.76)

\subsection{Exercise 4.9}
Let ${(X,d_X)}$ and ${(Y,d_Y)}$ be metric space, ${E\subset X}$, ${f}$ is a mapping from ${E}$ to ${Y}$, and ${p}$ be a limit point of ${E}$. Prove ${f}$ is continuous at ${p}$ if and only if
\[\lim\limits_{x\to p}f(x)=f(p)\]

\subsection{Exercise 4.11}
Let ${(X,d_X)}$ and ${(Y,d_Y)}$ be metric space. Assume that ${E\subset X}$, ${f}$ is a mapping from ${E}$ to ${Y}$, and ${p}$ is a point of ${E}$. prove
\[\lim\limits_{n\to\infty}f(p_n)=f(p)\]
for every sequence ${\{p_n\}}$ in ${E}$ such that
\[p_n\neq p,\lim\limits_{n\to\infty}p_n=p\]
if and only if
\[\lim\limits_{n\to\infty}f(p_n)=f(p)\]
for every sequence ${\{p_n\}}$ in ${E}$ such that
\[\lim\limits_{n\to\infty}p_n=p\]

\subsection{Exercise 4.16}
Let ${X,Y}$ be sets and ${f}$ be a function from ${X}$ to ${Y}$. Recall the following definitions: for any ${U\subset X}$,
\[f(U):=\{f(x)\in Y:x\in U\}\]
and for any ${V\subset Y}$,
\[f^{-1}(V):=\{x\in X:f(x)\in V\}\]
Prove the following properties:
\begin{enumerate}
\item{for any collection of subsets of ${X}$ ${\{U_\alpha:\alpha\in A\}}$,\[f\bigg(\bigcup\limits_{\alpha\in A}U_\alpha\bigg)=\bigcup\limits_{\alpha\in A}f(U_\alpha)\]}
\item{for any collection of subsets of ${Y}$ ${\{V_\alpha:\alpha\in A\}}$,\[f^{-1}\bigg(\bigcup\limits_{\alpha\in A}V_\alpha\bigg)=\bigcup\limits_{\alpha\in A}f^{-1}(V_\alpha)\]}
\item{for any ${U\subset X}$, ${f^{-1}(f(U))=U}$}
\item{for any ${V\subset Y}$, ${f(f^{-1}(V))\subset V}$\\Moreover, disprove that for any ${V\subset Y}$, \[f(f^{-1}(V))=V\]and find conditions to satisfy the above equation.}
\end{enumerate}

\subsection{Exercise 4.19}
Let ${(X,d_X)}$, ${(Y,d_Y)}$ be metric spaces and ${f}$ be a mapping from ${X}$ to ${Y}$. Assume that ${f}$ is continuous on ${X}$ and ${X}$ is compact. Prove or disprove the following two statements:
\begin{enumerate}
\item{${f}$ is bounded on ${X}$}
\item{the set ${\{f(x):x\in X\}}$ is closed in ${Y}$}
\end{enumerate}

\subsection{Exercise 4.23}
Let ${E\subset\mathbb{R}}$ and assume ${E\neq\emptyset}$. prove that there exists a sequence ${\{q_n\}}$ in ${E}$ such that
\[\lim\limits_{n\to\infty}q_n=\inf E\]

\subsection{Exercise 4.24}
Let ${X}$ be a metric space, ${E\subset X}$, ${\{a_n\}}$ be a sequence in ${E}$, and ${a\in X}$. Assume that
\[\lim\limits_{n\to\infty}a_n=a\]
and ${\{a_n:n\in\mathbb{N}\}}$ is infinite. Then ${a}$ is a limit point of ${E}$.

\subsection{Exercise 4.26}
Let ${E\subset\mathbb{R}}$. Prove ${\inf E\in\bar{E}}$.

\subsection*{Theorem 4.27}
Let ${X}$ be a metric space, ${f}$ be a mapping from ${X}$ to ${\mathbb{R}}$, and
\[M=\sup\limits_{p\in X}f(p):=\sup\{f(p):p\in X\}\]
and
\[m=\inf\limits_{p\in X}f(p):=\inf\{f(p):p\in X\}\]
Assume that ${f}$ is continuous on ${X}$ and ${X}$ is compact. Then there exist ${p,q\in X}$ such that
\[f(p)=M,f(q)=m\]

\subsection*{Theorem 3.13}
Let ${(X,d)}$ be a metric space and ${K\subset X}$. Assume that ${K}$ is compact. Then ${K}$ is sequentially compact.

\subsection*{Corollary 4.10}
Let ${(X,d_X)}$ and ${(Y,d_Y)}$ be metric spaces. Assume that ${E\subset X}$, ${f}$ is a mapping from ${E}$ to ${Y}$, and ${p}$ is a limit point of ${E}$. Then ${f}$ is continous at ${p}$.
\[\lim\limits_{x\to p}f(x)=f(p)\]
if and only if
\[\lim\limits_{n\to\infty}(f(p_n)=f(p)\]
for every sequence ${\{p_n\}}$ in ${E}$ such that
\[\lim\limits_{n\to\infty}p_n=p\]

\subsection{Exercise 4.28}
Prove Theorem 4.27 by using Theorem 3.13 and Corollary 4.10 instead of Theorem 4.17

\subsection{Exercise 4.29}
Let ${X}$ be a metric space, ${f}$ be a mapping from ${X}$ to ${\mathbb{R}}$, and
\[M=\sup\limits_{p\in X}f(p):=\sup\{f(p):p\in X\}\]
and
\[m=\inf\limits_{p\in X}f(p):=\inf\{f(p):p\in X\}\]
Prove or disprove the following four statements:
\begin{enumerate}
\item{if ${f}$ is bounded on ${X}$, then there exists ${p,q\in X}$ such that\[f(p)=M, f(q)=m\]}
\item{if the set ${\{f(p):p\in X\}\subset\mathbb{R}}$ is closed, then there exists ${p,q\in X}$ such that\[f(p)=M,f(q)=m\]}
\item{if the set ${\{f(p):p\in X\}\subset\mathbb{R}}$ is closed and bounded above, then there exists ${p \in X}$ such that\[f(p)=M\]}
\item{if the set ${\{f(p):p\in X\}\subset\mathbb{R}}$ is closed and bounded below, then there exists ${q \in X}$ such that\[f(q)=m\]}
\end{enumerate}

\subsection{Exercise 4.30}
Let ${X}$ be a compact metric space and ${f}$ be a continous mapping from ${X}$ to ${\mathbb{R}}$. Then both ${\max\{f(x):x\in X\}}$ and ${\min\{f(x):x\in X\}}$ exist.

\subsection{Exercise 4.36}
Let ${b\in(0,\infty)}$. Show that ${\log x}$ is not uniformly continous on ${0,b)}$. You can use the following properties of ${\log x}$:
\begin{enumerate}
\item{${\log x-\log y=\log{(\frac{x}{y})}}$  ${\forall x,y>0}$}
\item{${\lim\limits_{x\to 0+}\log x=-\infty}$\\i.e. for all ${M<0}$ there exists a ${\delta>0}$ such that\[\ln x<M \text{   }\forall 0<x<\delta\]}
\end{enumerate}
% ------------------------------------------------------------------------------ 
% End document
% ------------------------------------------------------------------------------

\end{document}