FROM pytorch/pytorch:2.1.1-cuda12.1-cudnn8-runtime

# We love UTF!
ENV LANG C.UTF-8
    
RUN set -x \
    && apt-get update \
	&& apt-get install -y git vim tmux nano htop sudo curl wget gnupg2 \
	&& apt-get install -y bash-completion \
    && apt-get install sudo -y

# Copy requirements.txt and install the Python packages
COPY requirements.txt /tmp/requirements.txt
# Enable pip caching via BuildKit (recommended)
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install --ignore-installed -r /tmp/requirements.txt

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y graphviz
RUN apt-get install python3-tk -y

RUN apt-get update && apt-get install -y libgl1

RUN set -x \
     && apt-get clean && rm -rf /var/lib/apt/lists/* \
	 && useradd -ms /bin/bash user \
	 && echo "user:user" | chpasswd && adduser user sudo \
	 && echo "user ALL=(ALL) NOPASSWD: ALL " >> /etc/sudoers

RUN pip3 uninstall -y psutil
RUN pip3 install psutil

USER user
WORKDIR /home/user
RUN mkdir notebooks

RUN python -m spacy download en_core_web_sm --direct

RUN git clone https://github.com/jimeh/tmux-themepack.git ~/.tmux-themepack  \
        && git clone https://github.com/tmux-plugins/tmux-resurrect ~/.tmux-resurrect
COPY --chown=user:user ./.tmux.conf /home/user/.tmux.conf
COPY --chown=user:user ./.powerline.sh /home/user/.powerline.sh

#
# Set some decent colors if the container needs to be accessed via /bin/bash.
RUN echo LS_COLORS=$LS_COLORS:\'di=1\;33:ln=36\' >> ~/.bashrc \
        && echo export LS_COLORS >> ~/.bashrc \
        && echo 'source ~/.powerline.sh' >> ~/.bashrc \
        && echo 'alias tmux="tmux -2"' >> ~/.bashrc \
        && echo 'PATH=~/bin:$PATH' >> ~/.bashrc \
        && touch ~/.sudo_as_admin_successful # To surpress the sudo message at run.


CMD sudo service ssh start && /bin/bash
