require 'rspec/core/rake_task'
require 'pact/tasks/verification_task'
require 'pact_broker/client/tasks'

LOCAL_PACT_BROKER_BASE_URL = 'http://localhost:9292'
REMOTE_PACT_BROKER_BASE_URL = 'https://test.pact.dius.com.au'

RSpec::Core::RakeTask.new('spec') do | task |
  task.pattern = "consumer/spec/bar_spec.rb"
end

PactBroker::Client::PublicationTask.new(:local) do | task |
  task.consumer_version = "1.0.0"
  task.pact_broker_base_url = LOCAL_PACT_BROKER_BASE_URL
  task.pattern = "consumer/spec/pacts/*.json"
end

PactBroker::Client::PublicationTask.new(:remote) do | task |
  task.consumer_version = "1.0.0"
  task.pact_broker_base_url = REMOTE_PACT_BROKER_BASE_URL
  task.pact_broker_basic_auth =  { username: ENV['PACT_BROKER_USERNAME'], password: ENV['PACT_BROKER_PASSWORD']}
  task.pattern = "consumer/spec/pacts/*.json"
end

Pact::VerificationTask.new('foobar') do | pact |
  pact.uri './consumer/spec/pacts/foo-bar.json', pact_helper: './provider/spec/pact_helper.rb'
end

Pact::VerificationTask.new(:foobar_using_local_broker) do | pact |
  pact.uri "#{LOCAL_PACT_BROKER_BASE_URL}/pacts/provider/Bar/consumer/Foo/version/1.0.0", :pact_helper => './provider/spec/pact_helper.rb'
end

Pact::VerificationTask.new(:foobar_using_remote_broker) do | pact |
  pact.uri "#{REMOTE_PACT_BROKER_BASE_URL}/pacts/provider/Bar/consumer/Foo/version/1.0.0", :pact_helper => './provider/spec/pact_helper.rb'
end

task 'pact:set_publish_verification_results_flag' do
  ENV['PUBLISH_VERIFICATIONS_RESULTS'] = 'true'
end

task 'pact:verify:foobar' => ['spec']
task 'pact:verify:foobar_using_local_broker' => ['pact:set_publish_verification_results_flag', 'spec', 'pact:publish:local']
task 'pact:verify:foobar_using_remote_broker' => ['pact:set_publish_verification_results_flag', 'spec', 'pact:publish:remote']

