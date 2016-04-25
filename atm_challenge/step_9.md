## Step 9 - The Person

At this step we want to create a Person class and give him 3 attributes: `:name`, `:cash` and `:account`. We will give you a basic set of specs for that class. Your job will be to implement the code that will make these specs pass. 

Remember, you are free to modify these specs if you find any flaws in it OR if you find another way of testing the same behavior. 

!FILENAME spec/person_spec.rb
```ruby
require './lib/person'
require './lib/atm'

describe Person do

  subject { described_class.new(name: 'Thomas') }

  it 'is expected to have a :name on initialize' do
    expect(subject.name).not_to be nil
  end

  it 'is expected to raise error if no name is set' do
    expect { described_class.new }.to raise_error 'A name is required'
  end

  it 'is expected to have a :cash attribute with value of 0 on initialize' do
    expect(subject.cash).to eq 0
  end

  it 'is expected to have a :account attribute' do
    expect(subject.account).to be nil
  end

  describe 'can create an Account' do
    # As a Person,
    # in order to be able to use banking services to manage my funds,
    # i would like to be able to create a bank account
    before { subject.create_account }
    it 'of Account class ' do
      expect(subject.account).to be_an_instance_of Account
    end

    it 'with himself as an owner' do
      expect(subject.account.owner).to be subject
    end
  end

  describe 'can manage funds if an account been created' do
    let(:atm) { Atm.new }
    # As a Person with a Bank Account,
    # in order to be able to put my funds in the account ,
    # i would like to be able to make a deposit
    before { subject.create_account }
    it 'can deposit funds' do
      expect(subject.deposit(100)).to be_truthy
    end

  describe 'can not manage funds if no account been created' do
    # As a Person without a Bank Account,
    # in order to prevent me from using the wrong bank account,
    # I should NOT be able to to make a deposit.
    it 'can\'t deposit funds' do
      expect { subject.deposit(100) }.to raise_error(RuntimeError, 'No account present')
    end
  end
end

```



