module GitHub

  extend FileUtils

  REPO_NAME = 'repo'
  MY_USERNAME = 'franklinyu'

  def self.backup(owner:, repo:, branch: 'HEAD')
    source_url = "https://github.com/#{owner}/#{repo}.git"
    backup_url = "git@github.com:#{MY_USERNAME}/#{repo}.git"
    if branch == 'HEAD'
      sh('git', 'clone', source_url, REPO_NAME)
    else
      sh('git', 'clone', '--branch', branch, source_url, REPO_NAME)
    end
    Dir.chdir(REPO_NAME) do
      sh('git', 'remote', 'add', 'backup', backup_url)
      sh('git', 'push', 'backup', branch)
    end
    sh('rm', '-r', '-f', REPO_NAME)
  end

end

SSH_DIRECTORY = "#{ENV['HOME']}/.ssh"

directory SSH_DIRECTORY

file "#{SSH_DIRECTORY}/id_rsa" => [SSH_DIRECTORY] do |task|
  raise 'private key not found' if ENV['PRIVATE_ID'].nil?
  File.open(task.name, 'w') do |file|
    file.write(ENV['PRIVATE_ID'])
  end
end

file "#{SSH_DIRECTORY}/known_hosts" => [SSH_DIRECTORY] do |task|
  raise 'GitHub public key not found' if ENV['GITHUB_PUBLIC_ID'].nil?
  File.open(task.name, 'w') do |file|
    file.write(ENV['GITHUB_PUBLIC_ID'])
  end
end

task backup: ["#{SSH_DIRECTORY}/id_rsa", "#{SSH_DIRECTORY}/known_hosts"] do
  GitHub.backup(owner: 'shadowsocks', repo: 'shadowsocks', branch: 'master')
end
