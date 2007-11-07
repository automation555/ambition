require 'rake'
require 'rake/testtask'
require 'rake/rdoctask'

Version = '0.5.0'

module Rake::TaskManager
  def delete_task(task_class, args, &block)
    task_name, deps = resolve_args(args)
    @tasks.delete(task_class.scope_name(@scope, task_name).to_s)
  end
end
class Rake::Task
  def self.delete_task(args, &block) Rake.application.delete_task(self, args, &block) end
end
def delete_task(args, &block) Rake::Task.delete_task(args, &block) end

begin
  require 'rubygems'
  gem 'echoe', '=1.3'
  ENV['RUBY_FLAGS'] = ""
  require 'echoe'

  Echoe.new('ambition', Version) do |p|
    p.rubyforge_name = 'err'
    p.summary        = "Ambition builds SQL from plain jane Ruby."
    p.description    = "Ambition builds SQL from plain jane Ruby."
    p.url            = "http://errtheblog.com/"
    p.author         = 'Chris Wanstrath'
    p.email          = "chris@ozmm.org"
    p.extra_deps << ['ParseTree',    '=2.0.1']
    p.extra_deps << ['ruby2ruby',    '=1.1.7']
    p.extra_deps << ['activerecord', '>=1.15.0']
    p.test_globs = 'test/*_test.rb'
  end

rescue LoadError 
end

delete_task :test

Rake::TestTask.new('test') do |t|
  t.pattern = 'test/*_test.rb'
end

Rake::TestTask.new('test:adapters') do |t|
  t.pattern = 'test/adapters/**/*_test.rb'
end

Dir['test/adapters/*'].each do |adapter|
  adapter = adapter.split('/').last
  Rake::TestTask.new("test:adapters:#{adapter}") do |t|
    t.pattern = "test/adapters/#{adapter}/*_test.rb"
  end
end

desc 'Default: run unit tests.'
task :default => :test

desc 'Generate RDoc documentation'
Rake::RDocTask.new(:rdoc) do |rdoc|
  files = ['README', 'LICENSE', 'lib/**/*.rb']
  rdoc.rdoc_files.add(files)
  rdoc.main     = "README" 
  rdoc.title    = "ambition"
  rdoc.template = File.exists?(t="/Users/chris/ruby/projects/err/rock/template.rb") ? t : "/var/www/rock/template.rb"
  rdoc.rdoc_dir = 'doc' 
  rdoc.options << '--inline-source'
end

desc 'Generate coverage reports'
task :rcov do
  `rcov -e gems test/*_test.rb`
  puts 'Generated coverage reports.'
end