<h2>Update system first</h2>
<pre><code>
sudo apt update && sudo apt upgrade
</code></pre>

<h5>Install Oh My Zsh</h5>
<pre><code>
sudo apt install zsh
sudo apt install powerline fonts-powerline
</code></pre>

<h5>Clone Oh my zsh repo</h5>
<pre><code>
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
</code></pre>

<h5>Create a New ZSH configuration file</h5>
<pre><code>
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
</code></pre>

<h5>Change zsh theme</h5>
<pre><code>
nano .zshrc
</code></pre>
Find the line <pre><code>ZSH_THEME="robbyrussell"</code></pre> and chnage it to <pre><code>ZSH_THEME="agnoster"</code></pre>

<h5>Change default shell</h5>
<pre><code>
chsh -s /bin/zsh
</code></pre>

<hr>
<h5>Update zsh</h5>
<pre><code>
cd .oh-my-zsh
upgrade_oh_my_zsh
</code></pre>

<h5>Change it back to default shell</h5>
<pre><code>
chsh -s /bin/bash
</code></pre>