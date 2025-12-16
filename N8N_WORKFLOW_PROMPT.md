# n8n Workflow Development and Testing Prompt

## Objective
Create a functional n8n workflow that meets specified requirements, then systematically test it against key criteria to ensure quality and reliability.

## Initial Requirements Gathering

Before starting development, clarify:

1. **Workflow Purpose**: What should this workflow accomplish?
2. **Trigger Type**: Manual, webhook, schedule, event-based?
3. **Data Flow**: What data enters, how is it processed, where does it go?
4. **Integration Points**: What services/APIs need to be connected?
5. **Success Criteria**: What defines a successful execution?
6. **Error Handling**: How should failures be managed?
7. **Performance Requirements**: Expected volume, speed, concurrency needs

## Development Process

### Phase 1: Workflow Creation

1. **Design the Flow**:
   - Map out nodes from trigger to output
   - Identify data transformations needed
   - Plan error handling branches
   - Consider edge cases

2. **Implement Incrementally**:
   - Start with core happy path
   - Add one node at a time
   - Test each addition before proceeding
   - Use Function nodes for complex transformations

3. **Configuration Best Practices**:
   - Use meaningful node names
   - Add notes/documentation to complex nodes
   - Leverage expressions for dynamic values
   - Store sensitive data in credentials
   - Use environment variables where appropriate

### Phase 2: Testing Framework

Test the workflow against these key criteria:

#### 1. **Functional Testing**
- [ ] Workflow triggers correctly
- [ ] Each node executes without errors
- [ ] Data transforms as expected
- [ ] Output matches requirements
- [ ] All branches/conditions work properly

#### 2. **Data Validation**
- [ ] Input data is validated/sanitized
- [ ] Required fields are present
- [ ] Data types are correct
- [ ] Transformations preserve data integrity
- [ ] Output format matches specification

#### 3. **Error Handling**
- [ ] Invalid inputs are caught and handled
- [ ] API failures trigger appropriate fallbacks
- [ ] Timeout scenarios are managed
- [ ] Error messages are clear and actionable
- [ ] Failed executions don't leave partial state

#### 4. **Edge Cases**
- [ ] Empty/null values handled
- [ ] Large datasets processed correctly
- [ ] Rate limits respected
- [ ] Duplicate requests handled appropriately
- [ ] Concurrent executions don't conflict

#### 5. **Performance**
- [ ] Execution time within acceptable range
- [ ] Memory usage reasonable
- [ ] No unnecessary API calls
- [ ] Batch processing used where applicable
- [ ] Workflow doesn't timeout

#### 6. **Security**
- [ ] Credentials stored securely
- [ ] Sensitive data not logged
- [ ] Input sanitized to prevent injection
- [ ] API keys not exposed in workflow
- [ ] Proper authentication/authorization

#### 7. **Maintainability**
- [ ] Node names are descriptive
- [ ] Complex logic is documented
- [ ] Workflow structure is logical
- [ ] Easy to debug and modify
- [ ] Version controlled if possible

## Testing Execution Plan

### Step 1: Unit Testing (Per Node)
For each node in the workflow:
1. Verify configuration is correct
2. Test with sample data
3. Validate output structure
4. Check error handling
5. Document expected behavior

### Step 2: Integration Testing
1. Run complete workflow with test data
2. Verify end-to-end functionality
3. Check data flow between nodes
4. Validate integrations work together
5. Test all conditional branches

### Step 3: Scenario Testing
Test specific scenarios:
- **Happy Path**: Everything works perfectly
- **Partial Failure**: One service fails mid-flow
- **Bad Input**: Invalid or malformed data
- **Rate Limiting**: Service throttles requests
- **Timeout**: Long-running operations
- **Concurrent Execution**: Multiple simultaneous runs

### Step 4: Validation Report
After testing, provide:
1. ✅ **Passed Criteria**: Which tests succeeded
2. ❌ **Failed Criteria**: Which tests failed and why
3. 🔧 **Fixes Applied**: Changes made to address failures
4. 📋 **Remaining Issues**: Known limitations or issues
5. 🎯 **Overall Status**: Ready for production or needs work

## Iteration and Improvement

If tests fail:
1. Identify root cause
2. Implement fix
3. Re-test affected criteria
4. Verify no regression in other areas
5. Document the change

Continue iterating until all critical criteria pass.

## Example Workflow Structure
```
Trigger (Webhook/Schedule/Manual)
  ↓
[Validate Input] → [Error Handler: Invalid Input]
  ↓
[Transform Data]
  ↓
[API Call] → [Error Handler: API Failure]
  ↓
[Process Response]
  ↓
[Conditional Logic]
  ├─→ [Branch A: Success Path]
  └─→ [Branch B: Alternative Path]
  ↓
[Format Output]
  ↓
[Send Result] → [Error Handler: Send Failure]
  ↓
[End]
```

## Deliverables

Upon completion, provide:
1. **Workflow JSON**: Exportable n8n workflow file
2. **Test Results**: Complete checklist with status
3. **Documentation**: How to use and troubleshoot
4. **Known Limitations**: Any constraints or requirements
5. **Next Steps**: Recommendations for improvements

## Usage Instructions

To use this prompt:
1. Copy the requirements gathering questions
2. Provide your specific workflow needs
3. Let Claude develop the workflow incrementally
4. Review test results and iterate as needed
5. Deploy when all critical criteria pass

---

**Remember**: Quality over speed. A well-tested workflow saves time and headaches in production.
