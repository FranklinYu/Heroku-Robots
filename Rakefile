require 'tmpdir'

module GitHub

  extend FileUtils

  MY_USERNAME = 'franklinyu'

  def self.backup(owner:, repo:, branch: 'HEAD')
    source_url = "https://github.com/#{owner}/#{repo}.git"
    backup_url = "git@github.com:#{MY_USERNAME}/#{repo}.git"
    Dir.mktmpdir do |dir|
      if branch == 'HEAD'
        sh('git', 'clone', source_url, dir)
      else
        sh('git', 'clone', '--branch', branch, source_url, dir)
      end
      chdir(dir) do
        sh('git', 'remote', 'add', 'backup', backup_url)
        sh('git', 'push', 'backup', branch)
      end
    end
  end

end

SSH_DIRECTORY = "#{ENV['HOME']}/.ssh"

directory SSH_DIRECTORY

file "#{SSH_DIRECTORY}/id_rsa" => [SSH_DIRECTORY] do |task|
  raise 'private key not found' if ENV['PRIVATE_ID'].nil?
  File.open(task.name, 'w') do |file|
    file.write(ENV['PRIVATE_ID'] + "\n")
  end
end

file "#{SSH_DIRECTORY}/known_hosts" => [SSH_DIRECTORY] do |task|
  cp('GitHub.pub', task.name)
end

task backup: ["#{SSH_DIRECTORY}/id_rsa", "#{SSH_DIRECTORY}/known_hosts"] do
  GitHub.backup(owner: 'shadowsocks', repo: 'shadowsocks', branch: 'master')
  GitHub.backup(owner: 'EFForg', repo: 'https-everywhere', branch: 'master')
end
