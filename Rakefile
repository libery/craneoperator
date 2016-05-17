require 'rake'
require 'httparty'

def version
  File.readlines('./VERSION').first.strip
end

def latest_hub_version
  taginfo        = JSON.parse(HTTParty.get("https://hub.docker.com/v2/repositories/parabuzzle/craneoperator/tags/").body)['results']
  tags = []
  taginfo.each do |tag|
    tags << tag['name']
  end
  (tags - ['latest']).sort.last
end

def next_version
  base           = version
  taginfo        = JSON.parse(HTTParty.get("https://hub.docker.com/v2/repositories/parabuzzle/craneoperator/tags/").body)['results']
  tags = []
  taginfo.each do |tag|
    tags << tag['name']
  end
  current_base   = tags.grep(/#{base}/)
  return "#{base}.0" if current_base.empty?
  build = current_base.sort.last.split('.').last.to_i + 1
  return "#{base}.#{build}"
end

task :tag do
  sh "docker tag -f parabuzzle/craneoperator:latest parabuzzle/craneoperator:#{next_version}"
end

task :push => :tag do
  sh "docker push parabuzzle/craneoperator:#{next_version}"
  sh "docker push parabuzzle/craneoperator:latest"
end

task :build do
  sh "npm install"
  sh "npm install webpack"
  sh "node_modules/webpack/bin/webpack.js"
  sh "docker build -t parabuzzle/craneoperator:latest ."
end

task :default => [:build, :push]